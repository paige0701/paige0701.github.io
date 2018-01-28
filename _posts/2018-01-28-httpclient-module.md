---
layout : post
title : HttpClient Module in Angular
categories : angular
---


When we communicate with the server, we use HTTP protocal.
And in angular app, we use Http (httpModule). But from Angular 4.3 +,
httpClient is out, providing simplified API for HTTP functionality.
HttpClient returns an Observable object instead of a Promise, which means
there is no need to parse the JSON response anymore.



In order to use httpClient, we need to import the HttpClientModule.

```ts
// app.module.ts

// need to import HttpModule or HttpClientModule to use it !!

@NgModule({
  declarations: [
    AppComponent,
  ],

  imports: [
    BrowserModule,
    HttpClientModule,
  ],

  exports : [

  ],
  providers: [AppService],
  bootstrap: [AppComponent]
})

export class AppModule { }


```



After importing the httpClientModule, you an inject it in services and components.

```ts
// service.ts

@Injectable()
export class abstractService() {


  url : string = 'http://localhost:4200/assets/cafeLists.json';
  constructor(private httpClientModule : HttpClientModule, private http : httpModule) {

  }

  // http 
  protected get() {
    this.http.get(url)
      .map((response: Response) => response.json())
      .subscribe((data) => {
        console.log(data);
    });

  }


  // httpClient
  protected get() { 
    return this.httpClientModule.get(url).subscribe((result) => {
      console.info('result ==> ', result); // Result will fetch the json data from below.
    })
  }


  // When you want a full response ? use observe option
  protected get() { 
    return this.httpClientModule.get(url, {observe : 'response'}).subscribe((result) => {
      console.info('result ==> ', result);  // returns headers, name, status and more!!
    })
  }


}

```


// cafeLists.json
```json
[
  {"Name":"Epic Espresso the Coffee Bar", "Country" : "Korea", "Address": "Sang wangsimri"},
  {"Name":"Conan Coffee", "Country" : "Korea", "Address": "Wangsimri"},
    {"Name":"Dukong Coffee", "Country" : "Korea", "Address": "Pangyo"}
]


```






