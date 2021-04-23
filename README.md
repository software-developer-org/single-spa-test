# single-spa-test

## Repo to Test single-spa.js

## Generate root conifg

To generate a root conig use this command :

```bash
npx create-single-spa --moduleType root-config
```

This will generate a complete root config and startpoint for the single-spa application.

## Register Application

To register a new application in your root-config you have to do the following steps:

### Create New Microforontend

To generate a new application you can use the single-spa cli command:

```bash
npx create-single-spa --moduleType app-parcel
```

You can select a name and framework of your choice.

### Register the Application in the Importmap

You can find the importmap in the index.ejs.

```html
.... <% if (isLocal) { %>
<script type="systemjs-importmap">
  {
    "imports": {
      "@single-spa/welcome": "https://unpkg.com/single-spa-welcome/dist/single-spa-welcome.js",
      "@software-developer/root-config": "//localhost:9000/software-developer-root-config.js",
      "angular-generator": "//localhost:4200/main.js" <--- your application!
    }
  }
</script>
<% } %> ....
```

In this example the angular app is mapped to loclahost but if you deploy your application you have to change it to the published url.

### Map Application Route

Now you have to tell your root-config when to load your application.
To do this you have to register it in your root-config.ts like this:

```typescript
....
registerApplication(
  "angular-generator", // reffernce name of your application
  () => System.import("angular-generator"), // this is he name single-spa searches in your importmap
  (location) => location.pathname.startsWith("/angular") // this the route for your application
);
....
```

For more information check the [single-spa docs](https://single-spa.js.org/docs/getting-started-overview#quick-start)

## Create a Angular Microfrontend

These steps are needed to create and use a angular application in your single-spa root config

### Create Angular Application

You can create a new application for single-spa in two ways:

-Use the single-spa cli and choose angular as a framework

```bash
npx create-single-spa --moduleType app-parcel
```

-Use the angular cli and add single-spa

```bash
ng new <application-name> --routing --prefix <application-name>
ng add single-spa-angular
```

It is important to use the prefix option if you are using more then one angular appliaction to avoid namspace conflicts

### ADD Router Configuration

In the angular router you have to set a baseroute in your app-routing.module.ts

```Typescript
....
// single-spa configuration: https://single-spa.js.org/docs/ecosystem-angular#configure-routes

const routes: Routes = [
  { path: '**', component: EmptyRouteComponent } // part of single-spa configuration
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule],
  providers: [{ provide: APP_BASE_HREF, useValue: '/' }] // part of single-spa configuration
})
....
```

### BrowserAnimationsModule

If you use the BrowserAnimationsModule you have to import it into your app.module.ts to prefend unmounting errors in your single-spa application.

```typescript
....
 imports: [
    BrowserModule,
    AppRoutingModule,
    BrowserAnimationsModule
  ],
....
```
