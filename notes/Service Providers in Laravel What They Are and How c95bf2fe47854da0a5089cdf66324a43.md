# Service Providers in Laravel: What They Are and How to Use Them

Created: April 24, 2022 9:25 PM
Finished: No
Source: https://laravel-news.com/service-providers

![Service%20Providers%20in%20Laravel%20What%20They%20Are%20and%20How%20c95bf2fe47854da0a5089cdf66324a43/service-providers.jpg](Service%20Providers%20in%20Laravel%20What%20They%20Are%20and%20How%20c95bf2fe47854da0a5089cdf66324a43/service-providers.jpg)

For those who haven't actively used Service Providers in Laravel, it's a mystical "term": what "service" do they actually "provide", and how exactly does it all work? I will explain it in this article.

## Default Laravel Service Providers

Let's start with the default service providers included in Laravel, they are all in the `app/Providers` folder:

- AppServiceProvider
- AuthServiceProvider
- BroadcastServiceProvider
- EventServiceProvider
- RouteServiceProvider

They are all PHP classes, each related to its topic: general "app", Auth, Broadcasting, Events, and Routes. But they all have one thing in common: the `boot()` method.

Inside of that method, you can write any code related to one of those sections: auth, events, routes, etc. In other words, Service Providers are just classes to register some global functionality.

They are separated as "providers" because they are executed really early in the Application Lifecycle, so it is convenient something global here before the execution script comes to Models or Controllers.

The most amount of functionality is in the RouteServiceProvider, let's take a look at its code:

```
 3    public const HOME = '/dashboard'; 5    public function boot() 7        $this->configureRateLimiting(); 9        $this->routes(function () {10            Route::prefix('api')11                ->middleware('api')12                ->group(base_path('routes/api.php'));14            Route::middleware('web')15                ->group(base_path('routes/web.php'));19    protected function configureRateLimiting()21        RateLimiter::for('api', function (Request $request) {22            return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
```

This is the class where route files are configured, with `routes/web.php` and `routes/api.php` included by default. Notice that for the API there are also different configurations: endpoint prefix `/api` and middleware `api` for all the routes.

You can edit those Service Providers however you want, they are not in the `/vendor` folder. Typical customization of this file happens when you have a lot of routes and you want to separate them in your custom file. You create `routes/auth.php` and put the routes there, and then you "enable" that file in the `boot()` method of the `RouteServiceProvider`, just add the third sentence:

Other default service providers have other functionality, you can analyze them by yourself. Except for `AppServiceProvider`, it is empty, like a placeholder for us to add any code related to some global application settings.

One popular example of adding code to the `AppServiceProvider` is about [disabling the lazy loading in Eloquent](https://laravel-news.com/disable-eloquent-lazy-loading-during-development). To do that, you just need to [add two lines](https://laravel.com/docs/9.x/eloquent-relationships#preventing-lazy-loading) into the `boot()` method:

```
4public function boot()6    Model::preventLazyLoading(! $this->app->isProduction());
```

This will throw an exception if some relationship model isn't eager loaded, which causes a so-called N+1 query problem with performance.

## When Service Providers Are Executed?

If you look at the [official docs about request lifecycle](https://laravel.com/docs/9.x/lifecycle), these are the things executed in the very beginning:

- public/index.php
- bootstrap/app.php
- app/Http/Kernel.php and its Middlewares
- Service Providers: exactly our topic of this article

Which providers are loaded? It's defined in the `config/app.php` array:

```
 5    'providers' => [11        Illuminate\Broadcasting\BroadcastServiceProvider::class,14        Illuminate\Validation\ValidationServiceProvider::class,20        App\Providers\AppServiceProvider::class,21        App\Providers\AuthServiceProvider::class,23        App\Providers\EventServiceProvider::class,24        App\Providers\RouteServiceProvider::class,
```

As you can see, there's a list of non-public service providers from the `/vendor` folder, you shouldn't touch/edit them. The ones we're interested in are at the bottom, with `BroadcastServicerProvider` disabled by default, probably because it's rarely used.

All of those service providers are executed from top to bottom, iterating that list **twice**.

The first iteration is looking for an optional method `register()` which may be used to initiate something before the `boot()` method. I've never used it in my experience.

Then, the second iteration executes the `boot()` method of all providers. Again, one by one, from top to bottom of the `'providers'` array.

And then, after all the service providers have been processed, Laravel goes to parsing the route, executing the Controller, using Models, etc.

## Create Your Custom Service Provider

In addition to the existing default files, you can easily create your service provider, related to some other topics than the default ones like auth/event/routes.

Quite a typical example is configuration related to Blade views. If you want to create your Blade directive, you can add that code into any service provider's `boot()` method, including the default `AppServiceProvider`, but quite often developers create a separate ViewServiceProvider.

You can generate it with this command:

It will generate the default template:

```
10     * @return void12    public function register()20     * @return void22    public function boot()
```

You may remove the `register()` method, and inside of `boot()` add Blade directive code:

```
6        return "<?php echo ($expression)->format('m/d/Y H:i'); ?>";
```

Another example of a ViewServiceProvider is about View Composers, here's the snippet [from the official Laravel docs](https://laravel.com/docs/9.x/views#view-composers):

```
 7    public function boot()10        View::composer('profile', ProfileComposer::class);13        View::composer('dashboard', function ($view) {
```

To be executed, this new provider should be added to the array of providers in `config/app.php`, as mentioned above:

```
 4    'providers' => [ 6        App\Providers\AppServiceProvider::class, 7        App\Providers\AuthServiceProvider::class, 9        App\Providers\EventServiceProvider::class,10        App\Providers\RouteServiceProvider::class,13        App\Providers\ViewServiceProvider::class,
```

## Examples from Open-Source Projects

Finally, I want to mention a few examples from freely available Laravel projects.

### 1. spatie/freek.dev: BladeComponentServiceProvider

A well-known company Spatie has published the source code for the personal blog of [Freek Van der Herten](https://twitter.com/freekmurze), with this file.

**app/Providers/BladeComponentServiceProvider.php**:

```
 9    public function boot()11        Blade::component('ad', AdComponent::class);13        Blade::component('front.components.inputField', 'input-field');14        Blade::component('front.components.submitButton', 'submit-button');15        Blade::component('front.components.textarea', 'textarea');16        Blade::component('front.components.textarea', 'textarea');17        Blade::component('front.components.shareButton', 'share-button');18        Blade::component('front.components.lazy', 'lazy');19        Blade::component('front.components.postHeader', 'post-header');21        Blade::component('front.layouts.app', 'app-layout');
```

### 2. monicahq/monica: MacroServiceProvider

One of the most-starred Laravel open-source projects has a separate file to register Collection macros:

**app/Providers/MacroServiceProvider.php**:

```
14    public function boot()16        if (! Collection::hasMacro('sortByCollator')) {17            Collection::macro('sortByCollator', function ($callback, $options = \Collator::SORT_STRING, $descending = false) {18                /** @var Collection */19                $collect = $this;21                return CollectionHelper::sortByCollator($collect, $callback, $options, $descending);25        if (! Collection::hasMacro('groupByItemsProperty')) {26            Collection::macro('groupByItemsProperty', function ($property) {27                /** @var Collection */28                $collect = $this;30                return CollectionHelper::groupByItemsProperty($collect, $property);34        if (! Collection::hasMacro('mapUuid')) {35            Collection::macro('mapUuid', function () {36                /** @var Collection */37                $collect = $this;39                return $collect->map(function ($item) {40                    return $item->uuid;
```

### 3. phpreel/phpreelcms: DashboardComponentsServiceProvider

Laravel CMS called phpReel also has a service provider for Blade components, named even longer.

**app/Providers/DashboardComponentsServiceProvider.php**:

```
29            $html = '<?php echo \'' . $component . '\'; ?>';31            return ('<?php echo "' . $component . '"; ?>');
```

You can also find a few more examples of Service Providers at my [LaravelExamples.com website](https://laravelexamples.com/tag/service-providers).

Filed in: