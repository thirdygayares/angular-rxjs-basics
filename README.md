Building a simple application that fetches data from an API and displays it in a component. We'll use RxJS operators like `Observable`, `BehaviorSubject`, and `catchError` to handle data streams and errors.

The tutorial covers:

1. **Introduction to RxJS in simple terms**
    
2. **Setting up a basic Angular app**
    
3. **Creating a simple API service using HttpClient**
    
4. **Handling API responses with RxJS Observables**
    
5. **Handling errors using RxJS**
    

# Introduction to RxJS

**RxJS** (Reactive Extensions for JavaScript) is a library used with **Angular** to handle asynchronous events and data streams. Think of it as a way to manage and respond to things happening in your app, like API responses, user inputs, or other async tasks.

* **Observable**: This is like a stream of data. It can emit multiple values over time (such as API data).
    
* **BehaviorSubject**: It's like an Observable but has an initial value and can emit new values when updated.
    
* **catchError**: It is used to catch and handle errors in the Observable stream.
    

# File Structure

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725955774130/ec52bc23-f854-4e09-9479-00f853ad50ad.png)

### Step-by-Step Code Explanation

# 1\. **Post Model**

This model represents the structure of a **Post** object that we will receive from the API.

```typescript
export interface Post {
  userId: number;
  id: number;
  title: string;
  body: string;
}
```

# 2\. **Post Service**

In this service, we will fetch the posts from a fake online API ([`jsonplaceholder.typicode.com`](http://jsonplaceholder.typicode.com)) using **HttpClient**.

* **getPosts()** method fetches data from the API as an **Observable** of an array of `Post[]`.
    

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from "@angular/common/http";
import { Observable } from "rxjs";
import { Post } from "../models/post.model";

@Injectable({
  providedIn: 'root'
})
export class PostService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/posts';

  constructor(private http: HttpClient) {}

  getPosts(): Observable<Post[]> {
    return this.http.get<Post[]>(this.apiUrl);
  }
}
```

# 3\. **App Component**

In this component, we will:

* Fetch the posts using the `PostService`.
    
* Use **RxJS** to handle the data and errors.
    
* Display the posts in the template.
    

##### app.component.ts

In the `ngOnInit` lifecycle hook, we subscribe to the `getPosts()` method and handle any errors using **catchError**.

```typescript
import { Component, OnInit } from '@angular/core';
import { BehaviorSubject, catchError, Observable } from "rxjs";
import { Post } from "./models/post.model";
import { PostService } from "./services/post.service";

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  posts$: Observable<Post[]> | undefined;

  private errorSubject = new BehaviorSubject<string | null>(null);
  error$ = this.errorSubject.asObservable();

  constructor(private postService: PostService) {}

  ngOnInit(): void {
    this.posts$ = this.postService.getPosts().pipe(
      catchError(err => {
        this.errorSubject.next(err.message);
        return [];
      })
    );
  }
}
```

# 4\. **Template (HTML)**

The template will display:

* A list of posts from the `posts$` observable.
    
* An error message if something goes wrong.
    

```typescript
<div *ngIf="error$ | async as error">
  <div>
    {{ error }}
  </div>
</div>

<ul *ngIf="posts$ | async as posts">
  <li *ngFor="let post of posts">
    <h3> {{ post.title }} </h3>
    <p> {{ post.body }} </p>
  </li>
</ul>
```

# 5\. **App Module**

The module is where we import required Angular libraries like `BrowserModule`, `RouterModule`, and `HttpClientModule`.

```typescript
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import {RouterModule, Routes} from '@angular/router';
import {CommonModule} from "@angular/common";
import {AppComponent} from "./app.component";
import {provideAnimationsAsync} from "@angular/platform-browser/animations/async";
import {PostService} from "./services/post.service";
import { provideHttpClient} from "@angular/common/http";

const routes: Routes = [
  { path: '', component: AppComponent },
];

@NgModule({
  imports: [
    CommonModule,
    BrowserModule,
    RouterModule.forRoot(routes, {enableTracing: true}),
  ],

  exports: [RouterModule],

  declarations: [
  AppComponent,
],
  providers: [
  provideAnimationsAsync(),
    PostService,
    provideHttpClient()
  ],

  bootstrap: [
  AppComponent
  ]
  
})
export class AppModule { }

```

### How the App Works

1. **Service**: The `PostService` fetches posts from the API using **HttpClient**.
    
2. **Component**: In `AppComponent`, we subscribe to the posts observable and display them in the HTML.
    
3. **Template**: The template displays the posts and also shows an error if something goes wrong using `BehaviorSubject` and `catchError`.
    

# **OUTPUT**

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1725955708707/7954cc4a-22c5-45dd-a9dd-d5079c9c658d.png)

A list of **posts**, each consisting of a title and body, fetched from an API [`jsonplaceholder.typicode.com`](http://jsonplaceholder.typicode.com), which provides mock data for testing purposes.

# GitHub Link:

%[https://github.com/thirdygayares/Angular-Components-Modules-Routing.git]
