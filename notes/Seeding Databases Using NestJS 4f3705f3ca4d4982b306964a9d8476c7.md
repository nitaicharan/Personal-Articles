# Seeding Databases Using NestJS

Created: June 21, 2022 9:19 AM
Finished: No
Source: https://medium.com/the-crowdlinker-chronicle/seeding-databases-using-nestjs-cd6634e8efc5
Tags: #nestjs

![1*bIjxjDfQz2_vXc9bbVPZKQ.png](Seeding%20Databases%20Using%20NestJS%204f3705f3ca4d4982b306964a9d8476c7/1bIjxjDfQz2_vXc9bbVPZKQ.png)

NestJS is a highly scalable NodeJS framework revolutionizing the building of server-side apps. Its ability to create [Application Contexts](https://docs.nestjs.com/application-context) allows developers to easily build micro-applications and take advantage of components and modules within that environment.

One can simply create a wrapper around Nest’s container that will hold all the instantiated classes. You can then grab an existing instance inside that wrapper and run functions inside it, such as clearing your app cache, rebuilding configuration files etc.

# What To Seed? 🤔

Any kind of static data that is commonly shared between entities or is mainly used for testing is what one must focus on when seeding. These can either be base entities like Languages, or entities like Admin User Accounts which are mainly required when your application involves Authentication and User Role Management 💁‍♂️.

Language(s) being one of the simplest entities to explain, is what we are going to focus on today.

# Directory Structure 📂

Nest promotes a strict modular approach, which we shall adhere to. This is how, we are going to store all files explained in this article.

```
src /
├── database
│   └── seeders
│       ├── seeder.module.ts
│       ├── seeder.ts
│       └── language
│           ├── data.ts
│           ├── languages.module.ts
│           └── languages.service.ts
├── models
│   └── language
│       ├── entities
│       │   └── language.entity.ts
│       └── interfaces
│           └── language.interface.ts
├── providers
│   └── database
│       └── mysql
│           └── provider.module.ts
└── seed.ts
```

# Providers

Before we start, we need to connect to the database. In this post, we are going to use [TypOrmModule to connect to a MySQL database](https://docs.nestjs.com/techniques/database), however, you can create your own providers in a similar fashion for [MongoDB using the MongooseModule](https://hackmd.io/p5KKglLuRLC0Us1yQxNjdg?both). If your application requires you to connect to multiple databases, consider reading about [Dynamic Modules](https://docs.nestjs.com/modules).

## provider.module.ts

```
/**
 * Import and provide base typeorm (mysql) related classes.
 *
 * @module
 */
@Module({
  imports: [
    TypeOrmModule.forRootAsync({
      imports: [MysqlConfigModule],
      useFactory: async (mysqlConfigService: MysqlConfigService) => ({
        type: 'mysql' as DatabaseType,
        host: mysqlConfigService.host,
        port: mysqlConfigService.port,
        username: mysqlConfigService.username,
        password: mysqlConfigService.password,
        database: mysqlConfigService.database,
        entities: [Language],
        synchronize: true,
      }),
      inject: [MysqlConfigService],
    } as TypeOrmModuleAsyncOptions),
  ],
})
export class MysqlDatabaseProviderModule {}
```

Notice how we are using my `Language` Model directly in the entities array. This is different than the technique mentioned [here](https://docs.nestjs.com/techniques/database), especially because of an issue generally faced when creating production builds shared on its [Github](https://github.com/nestjs/nest/issues/1018).

# Models & Interfaces

Binding the model with an interface is good practice. With Typescript support out of the box, Nest makes this super easy for us.

# Interfaces

This is a simple example of how we can create a language interface. There isn’t much to explain here, but if your Model has types etc, you can take advantage of [Enums](https://www.typescriptlang.org/docs/handbook/enums.html) and then [bind them to your Model](https://github.com/typeorm/typeorm/blob/master/docs/entities.md#enum-column-type).

## languages.interface.ts

```
/**
 * Language variable type declaration.
 *
 * @interface
 */
export interface ILanguage {
  name: string;
}
```

# Model / Entity

It’s always good to have timestamps in your model. Other than that, creating a model is relatively simple. You can learn more about how to create models using TypeORM here: [TypeORM’s documentation](https://github.com/typeorm/typeorm).

## languages.entity.ts

```
/**
 * Entity Schema for Languages.
 *
 * @class
 */
@Entity({
  name: 'languages',
})
export class Language implements ILanguage {
  @PrimaryGeneratedColumn()
  id: number;  @Column()
  name: string;  @Column({
    type: 'timestamp',
    default: () => 'CURRENT_TIMESTAMP',
  })
  createdAt: string;  @Column({
    type: 'timestamp',
    default: () => 'CURRENT_TIMESTAMP',
  })
  updatedAt: string;
}
```

# Data

To make things simpler, we will be saving all the Language data in a constant. However, if you plan on using a *faker*, consider taking a look at [Faker’s documentation](https://github.com/marak/Faker.js/).

## data.ts

```
export const languages: ILanguage[] = [
  { name: 'English' },
  { name: 'French' },
  { name: 'Spanish' },
  { name: 'Russian' },
  // ... and others ...
];
```

# Services & Modules

Services act as an intermediary between the controller and the repository. It’s a term which you will find most commonly used when you search the internet for **Service-Repository Pattern**. You can read more about how to create Modules in NestJS [here](https://docs.nestjs.com/modules).

# Language Service

Let’s start by creating the `LanguageService` which will take its repository as a dependency. The repository however is pre-provided to us by TypeORM itself, so we don’t have to create it.

## languages.services.ts

```
/**
 * Service dealing with language based operations.
 *
 * @class
 */
@Injectable()
export class LanguageSeederService {
  /**
   * Create an instance of class.
   *
   * @constructs
   *
   * @param {Repository<Language>} languageRepository
   */
  constructor(
    @InjectRepository(Language)
    private readonly languageRepository: Repository<Language>,
  ) {}  /**
   * Seed all languages.
   *
   * @function
   */
  create(): Array<Promise<Language>> {
    return languages.map(async (language: ILanguage) => {
      return await this.languageRepository
        .findOne({ name: language.name })
        .exec()
        .then(async dbLangauge => {
          // We check if a language already exists.
          // If it does don't create a new one.
          if (dbLangauge) {
            return Promise.resolve(null);
          }          return Promise.resolve(
            // or create(language).then(() => { ... });
            await this.languageRepository.save(language),
          );
        })
        .catch(error => Promise.reject(error));
    });
  }
}
```

# Language Module

The language module imports the `TypeOrmModule` for the Language entity along with its service that handles seeding of all languages.

## languages.module.ts

```
/**
 * Import and provide seeder classes for languages.
 *
 * @module
 */
@Module({
  imports: [TypeOrmModule.forFeature([Language])],
  providers: [LanguageSeederService],
  exports: [LanguageSeederService],
})
export class LanguageSeederModule {}
```

# Main Seeder Module

## seeder.module.ts

```
/**
 * Import and provide seeder classes.
 *
 * @module
 */
@Module({
  imports: [MysqlDatabaseProviderModule, LanguageSeederModule],
  providers: [Logger, Seeder],
})
export class SeederModule {}
```

Notice how our main MySQL provider module is imported here. Loading of all classes is asynchronous by default but you can make them synchronous by importing providers in the method defined in [NestJS’s documentation](https://docs.nestjs.com/fundamentals/async-providers).

# Seeder Provider

This is the final seeder class which has a `seed()` function that further calls the `create()` function in the `LanguageSeederService`.

## seeder.ts

```
@Injectable()
export class Seeder {
  constructor(
    private readonly logger: Logger,
    private readonly languageSeederService: LanguageSeederService,
  ) {}  async seed() {
    await this.languages()
      .then(completed => {
        this.logger.debug('Successfuly completed seeding users...');        Promise.resolve(completed);
      })
      .catch(error => {
        this.logger.error('Failed seeding users...');        Promise.reject(error);
      });
  }  async languages() {
    return await Promise.all(this.languageSeederService.create())
      .then(createdLanguages => {
        // Can also use this.logger.verbose('...');
        this.logger.debug(
          'No. of languages created : ' +
            // Remove all null values and return only created languages.
            createdLanguages.filter(
              nullValueOrCreatedLanguage => nullValueOrCreatedLanguage,
            ).length,
        );        return Promise.resolve(true);
      })
      .catch(error => Promise.reject(error));
  }
}
```

# Building Application Context & Running The Seeder

The final steps involve writing a bootstrap function which will use `NestFactory` to create an application context.

# Bootstrap Your Micro-App

## seed.ts

```
async function bootstrap() {
  NestFactory.createApplicationContext(SeederModule)
    .then(appContext => {
      const logger = appContext.get(Logger);
      const seeder = appContext.get(Seeder);      seeder
        .seed()
        .then(() => {
          logger.debug('Seeding complete!');
        })
        .catch(error => {
          logger.error('Seeding failed!');          throw error;
        })
        .finally(() => appContext.close());
    })
    .catch(error => {
      throw error;
    });
}
bootstrap();
```

Notice how we have `appContext().close()` as the final promise action.

# The Command

Last but not the least, simply add this script to your `package.json`. You can then run your seeder using `npm` or `yarn` or any package manager you are using.

## package.json

```
{
  // ... other package.json fields ...
  "scripts": {
    // ... other scripts ...
    "seed": "ts-node -r tsconfig-paths/register src/seed.ts"
  }
}
```

# Conclusion

Nest is a really powerful back-end framework. I strongly believe that it is going to transform coding in NodeJS. For more information on Nest, please visit its [website](https://nestjs.com/) and consider [supporting them](https://docs.nestjs.com/support) on OpenCollective or via Paypal.

Also, be sure to check out the next part of this article at ‘[Adding Options to your Database Seeder](https://medium.com/the-crowdlinker-chronicle/adding-options-to-your-database-seeder-nestjs-768beecbaba8)’.

Let me know in the comments if you face any issues running this or need any other kind of assistance.