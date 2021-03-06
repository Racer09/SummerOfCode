# Week 4: Your Heroes on your Phone :iphone:

## Day 1: Go Mobile :busstop:
Put your app on your phone!
 - Install the NativeScript command line interface
   ```
   sudo npm install --global nativescript
   ```
 - We need to perform two extra steps because NativeScript is not yet fully compatible with Angular 10 (at the time of writing)
   - Remove the first line (as below) from the files tsconfig.app.json and tsconfig.base.json
     ```
     /* To learn more about this file see: https://angular.io/config/tsconfig. */
     ```
   - Install the Angular 9 version of schematics
     ```
     npm install --save-dev @schematics/angular@^9
     ```
 - Add NativeScript to your app
   ```
   ng add @nativescript/schematics
   ```
 - Run your app to check that it still works as before in the web browser
 - Now we need a couple more tweaks and we're good to go 
   - Install version 5 of the copy-webpack-plugin 
     ```
     npm install --save copy-webpack-plugin@^5
     ```
   - Add the following two lines to the end of the "compilerOptions" section in tsconfig.tns.json
     ```
     "baseUrl": "./",
     "experimentalDecorators": true
     ```
     - You will also need to add a comma at the end of the preceding line which contains a single curly bracket
 - Build the NativeScript app
   ```
   tns preview
   ```
   - This will display a QR code on your screen
 - To preview your app on your phone, you need to install two companion apps on your Android/iOS device(s):
   - NativeScript Playground ([Android](https://play.google.com/store/apps/details?id=org.nativescript.play), [iOS](https://apps.apple.com/us/app/nativescript-playground/id1263543946)) — used to scan a QR code provided by the NativeScript CLI
   - NativeScript Preview ([Android](https://play.google.com/store/apps/details?id=org.nativescript.preview), [iOS](https://apps.apple.com/us/app/nativescript-preview/id1264484702) — used to host display your app
 - Scan the QR code with the NativeScript Playground app, which will connect your project with the NativeScript Preview app
   - If everything works, you will see the following on your phone: "auto-generated works!"
 - As always commit and synchronize your changes

## Day 2: Move your Heroes :dash:  
We want our heroes to work in the web browser and the mobile app.
 - Make sure you read and follow these instructions carefully!
 - Migrate the app navigation
   - Add a shared file with the routes configuration in src/app: app.routes.ts
     ```
     import { Routes } from '@angular/router';
     ```
   - Cut and paste this code block from app-routing-module.ts to app.routes.ts
     ```
     const routes: Routes = [
       ...
     ];
     ```
     - We also need to export the routes, so change the first line to
       ```
       export const routes: Routes = [
       ```
   - Also cut and paste these import statements from app-routing-module.ts to app.routes.ts
     ```
     import { DashboardComponent }   from './dashboard/dashboard.component';
     import { HeroesComponent }      from './heroes/heroes.component';
     import { HeroDetailComponent }  from './hero-detail/hero-detail.component';
     ```
   - Add the following import to app-routing-module.ts
     ```
     import { routes } from './app.routes';
     ```
     - Change this line from
       ```
       import { RouterModule, Routes } from '@angular/router';
       ```
     - To
       ```
       import { RouterModule } from '@angular/router';
       ```
   - Remove this code block from app-routing-module.tns.ts
     ```
     export const routes: Routes = [
       ...
     ];
     ```
   - Add the following import to app-routing-module.tns.ts
     ```
     import { routes } from './app.routes';
     ```
     - And remove these two
       ```
       import { Routes } from '@angular/router';
       import { AutoGeneratedComponent } from '@src/app/auto-generated/auto-generated.component';
       ```
 - To get rid of the warning regarding 'Experimental support for decorators'
   - Go to Preferences -> Settings in Visual Studio Code
   - Enter 'decorator' into the search box
   - Enable the option for experimentalDecorators 
 - Commit and synchronize your changes
   - We won't be reminding you about this anymore
   - By now you surely noticed how useful it is to compare against the last working version in the Source Control tab of Visual Studio
   
## Day 3: Still Moving :sweat_smile:
Today we'll prepare our components for the mobile app.
 - Remove the auto-generated component
   - Delete the complete folder src/app/auto-generated in Visual Studio
   - In app.module.ts and app.module.tns.ts remove the following two lines
     ```
     import { AutoGeneratedComponent } from '@src/app/auto-generated/auto-generated.component';
     AutoGeneratedComponent,
     ```
 - Migrate the components
   ```
   ng g migrate-component --name=dashboard
   ng g migrate-component --name=hero-detail
   ng g migrate-component --name=heroes
   ```
 - If you open the file app.module.tns.ts, you will notice that all imports starting with '@src/app/..' are marked as invalid.
   - This is a Visual Studio Code issue and won't affect your app.
 - From the Extensions tab in Visual Studio install the [NativeScript extension](https://marketplace.visualstudio.com/items?itemName=NativeScript.nativescript)
 - Let's see what our mobile app looks like now
   ```
   tns preview
   ```
   - Scan the QR code using the NativeScript Playground app

## Day 4: App User Interface :octocat:
Our web app is quite nice, but we want our mobile app to feel like an app, not a web site.
 - In main.tns.ts replace
   ```
   platformNativeScriptDynamic().bootstrapModule(AppModule);
   ```
   - With
     ```
     platformNativeScriptDynamic({createFrameOnBootstrap: true}).bootstrapModule(AppModule);
     ```
 - Replace the first line of app.component.tns.html with these
   ```
   <ActionBar title="{{title}}">
     <ActionItem text="Dashboard" (tap)="dashboard()"></ActionItem>
     <ActionItem text="Heroes" (tap)="heroes()"></ActionItem>
   </ActionBar>
   ```
 - In app.component.tns.ts
   - After this line
     ```
     import { Component } from '@angular/core';
     ```
     - Add
       ```
       import { Router } from '@angular/router';
       ```
   - After this line
     ```
     export class AppComponent { }
     ```
     - Add
       ```
       constructor(private router: Router) {
       }
       
       dashboard() {
         this.router.navigateByUrl("/dashboard");
       }
       
       heroes() {
         this.router.navigateByUrl("/heroes");
       }
       ```
     - Remove the closing curly bracket } from
       ```
       export class AppComponent { }
       ```
     - And add it on a new line after the code block you just inserted
     - Use the tab key to indent the new code block by two spaces
 - Start the mobile app
   ```
   tns preview
   ```
 - Replace the content of dashboard.component.tns.html with
   ```
   <StackLayout>
     <Label text="Top Heroes" textAlignment="center" class="h3"></Label>/>
     <StackLayout orientation="horizontal">
       <Button *ngFor="let hero of heroes" textAlignment="center" class="h4" text="{{hero.name}}" nsRouterLink="/detail/{{hero.id}}"></Button>
     </StackLayout>
   </StackLayout>
   ```
 - Restart the mobile app if necessary
 - Replace hero-detail.component.tns.html with
   ```
   <StackLayout *ngIf="hero">
     <Label text="{{hero.name | uppercase}} Details" class="h2"></Label>
     <Label text="id: {{hero.id}}"></Label>
     <Label text="name:"></Label>
     <TextField [text]="hero.name"></TextField>
   </StackLayout>
   ```
   
 ## Day 5: Mobile List of Heroes :scroll:
 We will use a native list view component to display your heroes on your phone.
 - Install the NativeScript ListView from the terminal
   ```
   tns plugin add nativescript-ui-listview@rc
   ```
   - At the time of writing we need to use the release candidate (rc) version for compatibility with Angular 10
 - Edit app.module.tns.ts
   - After this line
     ```
     import { NativeScriptModule } from '@nativescript/angular';
     ```
     - Add
       ```
       import { NativeScriptUIListViewModule } from "nativescript-ui-listview/angular";
       ```
   - After
     ```
     imports: [
       NativeScriptModule,
     ```
     - Add
       ```
       NativeScriptUIListViewModule,
       ```
 - Replace heroes.component.tns.html with
   ```   
   <StackLayout>
     <Label text="My Heroes" class="h2"></Label>/>
     <ListView [items]="heroes" (itemTap)="onSelectHero($event)">
       <ng-template let-hero="item">
         <StackLayout orientation="horizontal">
           <Label textAlignment="right" [text]="hero.id"></Label>
           <Label textAlignment="left" [text]="hero.name"></Label>  
         </StackLayout>
       </ng-template>
     </ListView>
   </StackLayout>
   ```
 - In heroes.component.ts
   - Add the following imports
     ```
     import { Router } from '@angular/router';
     import { ItemEventData } from '@nativescript/core/ui/list-view/list-view';
     ```
   - Change the constructor to
     ```
     constructor(private heroService: HeroService, private router: Router) { }
     ```
   - After the method getHeroes() add a new method
     ```
     onSelectHero(event: ItemEventData) {
       const hero = this.heroes[event.index];
       this.router.navigateByUrl("/detail/" + hero.id);
     }
     ```
 - Test your mobile app and verify that everything works like in the web version
 
