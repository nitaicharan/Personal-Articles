# Angular 12 Refresh Token with Interceptor and JWT example

Created: August 26, 2022 6:39 PM
Finished: No
Source: https://www.bezkoder.com/angular-12-refresh-token/
Tags: #angular

With previous posts, we’ve known how to build Authentication and Authorization in a Angular 12 Application. In this tutorial, I will continue to show you way to implement Angular 12 Refresh Token before Expiration with Http Interceptor and JWT.

Related Posts:
 – [In-depth Introduction to JWT-JSON Web Token](https://bezkoder.com/jwt-json-web-token/)
 – [Angular 12 Login and Registration example with JWT & Web Api](https://www.bezkoder.com/angular-12-jwt-auth/)

Older version:
 – [Angular 11](https://www.bezkoder.com/angular-11-jwt-refresh-token/)
 – [Angular 10](https://www.bezkoder.com/angular-10-refresh-token/)

- [Conclusion](https://www.bezkoder.com/angular-12-refresh-token/#Conclusion)
- [Further Reading](https://www.bezkoder.com/angular-12-refresh-token/#Further_Reading)

## Angular 12 Refresh Token with Interceptor and JWT overview

The diagram shows flow of how we implement Angular 12 JWT Refresh Token with Http Interceptor example.

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-example-flow.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-example-flow.png)

– A `refreshToken` will be provided at the time user signs in.
 – A legal `JWT` must be added to HTTP Header if Angular 12 Client accesses protected resources.
 – With the help of Http Interceptor, Angular App can check if the `accessToken` (JWT) is expired (**401**), sends `/refreshToken` request to receive new `accessToken` and use it for new resource request.

Let’s see how the Angular Refresh Token example works with demo UI.

– User signs in with a legal account first.

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-login.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-login.png)

– Now the user can access resources with provided Access Token.

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-resources-ui.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-resources-ui.png)

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-resources.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-resources.png)

– When the Access Token is expired, Angular automatically sends Refresh Token request, receives new Access Token and uses it for new request.

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-token-expired.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-access-token-expired.png)

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-refresh-token.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-refresh-token.png)

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token.png)

So the server still accepts resource access from the user.

– After a period of time, the new Access Token is expired again, and the Refresh Token too. Our App forces logout the user.

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token-expired-ui.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token-expired-ui.png)

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token-expired.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-new-access-token-expired.png)

![Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-refresh-token-expired.png](Angular%2012%20Refresh%20Token%20with%20Interceptor%20and%20JWT%20%205668a57b44b24b6cbf211bd66a8f9e1b/angular-12-refresh-token-jwt-interceptor-refresh-token-expired.png)

The Back-end server for this Angular 12 Client can be found at:

We’re gonna implement Token Refresh feature basing on the code from previous posts, so you need to read following tutorial first:[Angular 12 Login and Registration example with JWT & Web Api](https://www.bezkoder.com/angular-12-jwt-auth/)

## Add Refresh Token function in Angular Service

Firstly, we need to create `refreshToken()` function that uses `HttpClient` to send HTTP Request with `refreshToken` in the body.

**_services**/*auth.service.ts*

```
import { Injectable } from '@angular/core';
import { HttpClient, HttpHeaders } from '@angular/common/http';
import { Observable } from 'rxjs';
const AUTH_API = 'http://localhost:8080/api/auth/';
const httpOptions = {
  headers: new HttpHeaders({ 'Content-Type': 'application/json' })
};
@Injectable({
  providedIn: 'root'
})
export class AuthService {
  constructor(private http: HttpClient) { }
  // login, register
  refreshToken(token: string) {
    return this.http.post(AUTH_API + 'refreshtoken', {
      refreshToken: token
    }, httpOptions);
  }
}

```

We also need to update `token-storage.service.ts` which provides get, set, remove methods to work with Access Token, Refresh Token and User Data stored on Browser.

**_services**/*token.service.ts*

```
import { Injectable } from '@angular/core';
const TOKEN_KEY = 'auth-token';
const REFRESHTOKEN_KEY = 'auth-refreshtoken';
const USER_KEY = 'auth-user';
@Injectable({
  providedIn: 'root'
})
export class TokenStorageService {
  constructor() { }
  signOut(): void {
    window.sessionStorage.clear();
  }
  public saveToken(token: string): void {
    window.sessionStorage.removeItem(TOKEN_KEY);
    window.sessionStorage.setItem(TOKEN_KEY, token);
    const user = this.getUser();
    if (user.id) {
      this.saveUser({ ...user, accessToken: token });
    }
  }
  public getToken(): string | null {
    return window.sessionStorage.getItem(TOKEN_KEY);
  }
  public saveRefreshToken(token: string): void {
    window.sessionStorage.removeItem(REFRESHTOKEN_KEY);
    window.sessionStorage.setItem(REFRESHTOKEN_KEY, token);
  }
  public getRefreshToken(): string | null {
    return window.sessionStorage.getItem(REFRESHTOKEN_KEY);
  }
  public saveUser(user: any): void {
    window.sessionStorage.removeItem(USER_KEY);
    window.sessionStorage.setItem(USER_KEY, JSON.stringify(user));
  }
  public getUser(): any {
    const user = window.sessionStorage.getItem(USER_KEY);
    if (user) {
      return JSON.parse(user);
    }
    return {};
  }
}

```

## Angular 12 Refresh Token with Interceptor

To implement refresh token, we need to follow 2 steps:

- save the Refresh Token right after making login request (which returns Access Token and Refresh Token).
- use Angular `HttpInterceptor` to check `401` status in the response and call `AuthService.refreshToken()` with saved Refresh Token above.

### Save Refresh Token after Login

In `LoginComponent`, we update `onSubmit()` functiob with new `TokenStorageService`‘s `saveRefreshToken()` method.

**login**/*login.component.ts*

```
...
import { TokenStorageService } from '../_services/token-storage.service';
...
export class LoginComponent implements OnInit {
  ...
  constructor(private authService: AuthService, private tokenStorage: TokenStorageService) { }
  ...
  onSubmit(): void {
    const { username, password } = this.form;
    this.authService.login(username, password).subscribe(
      data => {
        this.tokenStorage.saveToken(data.accessToken);
        this.tokenStorage.saveRefreshToken(data.refreshToken);
        this.tokenStorage.saveUser(data);
        ...
      },
      err => {
        this.errorMessage = err.error.message;
        this.isLoginFailed = true;
      }
    );
  }
  ...
}

```

### Angular Http Interceptor with 401 status for Refresh Token

We’re gonna silent refresh JWT Token using Angular `HttpInterceptor` when receiving response with status code `401`.

**_helpers**/*auth.interceptor.ts*

```
import { HTTP_INTERCEPTORS, HttpEvent, HttpErrorResponse } from '@angular/common/http';
import { Injectable } from '@angular/core';
import { HttpInterceptor, HttpHandler, HttpRequest } from '@angular/common/http';
import { TokenStorageService } from '../_services/token-storage.service';
import { AuthService } from '../_services/auth.service';
import { BehaviorSubject, Observable, throwError } from 'rxjs';
import { catchError, filter, switchMap, take } from 'rxjs/operators';
// const TOKEN_HEADER_KEY = 'Authorization';  // for Spring Boot back-end
const TOKEN_HEADER_KEY = 'x-access-token';    // for Node.js Express back-end
@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  private isRefreshing = false;
  private refreshTokenSubject: BehaviorSubject<any> = new BehaviorSubject<any>(null);
  constructor(private tokenService: TokenStorageService, private authService: AuthService) { }
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<Object>> {
    let authReq = req;
    const token = this.tokenService.getToken();
    if (token != null) {
      authReq = this.addTokenHeader(req, token);
    }
    return next.handle(authReq).pipe(catchError(error => {
      if (error instanceof HttpErrorResponse && !authReq.url.includes('auth/signin') && error.status === 401) {
        return this.handle401Error(authReq, next);
      }
      return throwError(error);
    }));
  }
  private handle401Error(request: HttpRequest<any>, next: HttpHandler) {
    if (!this.isRefreshing) {
      this.isRefreshing = true;
      this.refreshTokenSubject.next(null);
      const token = this.tokenService.getRefreshToken();
      if (token)
        return this.authService.refreshToken(token).pipe(
          switchMap((token: any) => {
            this.isRefreshing = false;
            this.tokenService.saveToken(token.accessToken);
            this.refreshTokenSubject.next(token.accessToken);

            return next.handle(this.addTokenHeader(request, token.accessToken));
          }),
          catchError((err) => {
            this.isRefreshing = false;

            this.tokenService.signOut();
            return throwError(err);
          })
        );
    }
    return this.refreshTokenSubject.pipe(
      filter(token => token !== null),
      take(1),
      switchMap((token) => next.handle(this.addTokenHeader(request, token)))
    );
  }
  private addTokenHeader(request: HttpRequest<any>, token: string) {
    /* for Spring Boot back-end */
    // return request.clone({ headers: request.headers.set(TOKEN_HEADER_KEY, 'Bearer ' + token) });
    /* for Node.js Express back-end */
    return request.clone({ headers: request.headers.set(TOKEN_HEADER_KEY, token) });
  }
}
export const authInterceptorProviders = [
  { provide: HTTP_INTERCEPTORS, useClass: AuthInterceptor, multi: true }
];

```

In the code above, we:
 – intercept requests or responses before they are handled by `intercept()` method.
 – handle `401` status on interceptor response (except response of `/login` request)
 – use `refreshTokenSubject` to track the current refresh token value. It is `null` if no token is currently available.
 For example, when the refresh progress is processing (`isRefreshing = true`), we will wait until `refreshTokenSubject` has a non-null value (new Access Token is ready and we can retry the request again).

## How to handle Token expiration in Angular 12

We will dispatch `logout` event to `App` component when response status tells us the access token is expired and refresh token too.

First we need to set up a global event-driven system, or a PubSub system, which allows us to listen and dispatch (emit) events from independent components so that they don’t have direct dependencies between each other.

We’re gonna create `EventBusService` with three methods: `on`, and `emit`.

**_shared**/*event-bus.service.ts*

```
import { Injectable } from '@angular/core';
import { Subject, Subscription } from 'rxjs';
import { filter, map } from 'rxjs/operators';
import { EventData } from './event.class';
@Injectable({
  providedIn: 'root'
})
export class EventBusService {
  private subject$ = new Subject<EventData>();
  constructor() { }
  emit(event: EventData) {
    this.subject$.next(event);
  }
  on(eventName: string, action: any): Subscription {
    return this.subject$.pipe(
      filter((e: EventData) => e.name === eventName),
      map((e: EventData) => e["value"])).subscribe(action);
  }
}

```

**_shared**/*event.class.ts*

```

export class EventData {
  name: string;
  value: any;
  constructor(name: string, value: any) {
    this.name = name;
    this.value = value;
  }
}

```

Now you can emit `event` to the bus and if any listener was registered with the `eventName`, it will execute the callback function `action`.

Next we import `EventBusService` in App component and listen to `"logout"` event.

**src**/*app.component.ts*

```
import { Component, OnInit, OnDestroy } from '@angular/core';
import { Subscription } from 'rxjs';
import { TokenStorageService } from './_services/token-storage.service';
import { EventBusService } from './_shared/event-bus.service';
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {
  private roles: string[] = [];
  isLoggedIn = false;
  showAdminBoard = false;
  showModeratorBoard = false;
  username?: string;
  eventBusSub?: Subscription;
  constructor(private tokenStorageService: TokenStorageService, private eventBusService: EventBusService) { }
  ngOnInit(): void {
    this.isLoggedIn = !!this.tokenStorageService.getToken();
    if (this.isLoggedIn) {
      const user = this.tokenStorageService.getUser();
      this.roles = user.roles;
      this.showAdminBoard = this.roles.includes('ROLE_ADMIN');
      this.showModeratorBoard = this.roles.includes('ROLE_MODERATOR');
      this.username = user.username;
    }
    this.eventBusSub = this.eventBusService.on('logout', () => {
      this.logout();
    });
  }
  ngOnDestroy(): void {
    if (this.eventBusSub)
      this.eventBusSub.unsubscribe();
  }
  logout(): void {
    this.tokenStorageService.signOut();
    this.isLoggedIn = false;
    this.roles = [];
    this.showAdminBoard = false;
    this.showModeratorBoard = false;
  }
}

```

Finally we only need to emit `"logout"` event in the components when getting `Unauthorized` response status (403).

**components**/*board-user.component.ts*

```
import { Component, OnInit } from '@angular/core';
import { UserService } from '../_services/user.service';
import { EventBusService } from '../_shared/event-bus.service';
import { EventData } from '../_shared/event.class';
...
export class BoardUserComponent implements OnInit {
  content?: string;
  constructor(private userService: UserService, private eventBusService: EventBusService) { }
  ngOnInit(): void {
    this.userService.getUserBoard().subscribe(
      data => { ... },
      err => {
        this.content = err.error.message || err.error || err.message;
        if (err.status === 403)
          this.eventBusService.emit(new EventData('logout', null));
      }
    );
  }
}

```

## Conclusion

Today we know how to implement Angular 12 JWT Refresh Token before expiration using Http Interceptor with 401 status code. For your understanding the logic flow, you should read one of following tutorial first:[Angular 12 Login and Registration example with JWT & Web Api](https://www.bezkoder.com/angular-12-jwt-auth/)

The Back-end server for this Angular 12 Client can be found at:

## Further Reading

Fullstack:

## Source Code

You can find the complete source code for this tutorial on [Github](https://github.com/bezkoder/angular-12-jwt-refresh-token).