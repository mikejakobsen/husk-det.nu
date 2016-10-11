Get started with Angular Material 2 using the Angular CLI.

## Install the CLI
 
 ```bash
 npm install -g angular-cli
 ```
 
## Create a new project
 
 ```bash
 ng new my-project
 ```

The AngularJS framework consists of several libraries, some of them core and some optional.

You write Angular applications by composing HTML templates with Angularized markup, writing component classes to manage those templates, adding application logic in services, and boxing components and services in modules.

![AngularJs](https://angular.io/resources/images/devguide/architecture/overview2.png)


## Modules

Angular modules/NgModules

An Angular module, whether a root or feature, is a class with an `@NgModule` decorator.

`@NgModule` is a decorator function that takes a single metadata object whose properties describe the module. The most important properties are.

		`declarations` - the view classes that belong to this module. Angular has three kinds of view classes: components, directives, and pipes.

		`exports` - the subset of declarations that should be visible and usable in the component templates of other modules.

		`imports` - other modules whose exported classes are needed by component templates declared in this module.

		`providers` - creators of services that this module contributes to the global collection of services; they become accessible in all parts of the app.

		`bootstrap` - the main application view, called the root component, that hosts all other app views. Only the root module should set this bootstrap property.

```javascript
	import { NgModule }      from '@angular/core';
	import { BrowserModule } from '@angular/platform-browser';
	@NgModule({
	imports:      [ BrowserModule ],
	providers:    [ Logger ],
	declarations: [ AppComponent ],
	exports:      [ AppComponent ],
	bootstrap:    [ AppComponent ]
	})
	export class AppModule { }
```

## Angular libraries

Each Angular library name begins with the `@angular` prefix.

You install through `NPM` and import parts of them with JavaScript import statements.

```javascript
import { BrowserModule } from '@angular/platform-browser';
```

In the example of the simple root module above, the application module needs material from within that BrowserModule. To access that material, add it to the `@NgModule` metadata imports like this.

```javascript
imports:      [ BrowserModule ],
````

In this way you're using both the Angular and JavaScript module systems together.

## Components

A component controls a patch of screen called a view.

```JavaScript
	export class HeroListComponent implements OnInit {
	heroes: Hero[];
	selectedHero: Hero;

	constructor(private service: HeroService) { }

	ngOnInit() {
		this.heroes = this.service.getHeroes();
	}

	selectHero(hero: Hero) { this.selectedHero = hero; }
	}
```

For example, this HeroListComponent has a heroes property that returns an array of heroes that it acquires from a service. 

HeroListComponent also has a selectHero() method that sets a selectedHero property when the user clicks to choose a hero from that list.

Angular creates, updates, and destroys components as the user moves through the application. `ngOnClick`, `ngOnInit`.

## Templates

You define a template along with the component. A template is a form of HTML that tells Angular how to render the component.

![Components](https://angular.io/resources/images/devguide/architecture/component-tree.png)

## Metadata

![Meta](https://angular.io/resources/images/devguide/architecture/template-metadata-component.png)

The metadata in the @Component tells Angular where to get the major building blocks you specify for the component.

The template, metadata, and component together describe a view.

Apply other metadata decorators in a similar fashion to guide Angular behavior. @Injectable, @Input, and @Output are a few of the more popular decorators.

## Data Binding

Without a framework, you would be responsible for pushing data values into the HTML controls and turning user responses into actions and value updates. Fu**ing Old school..

![img](https://angular.io/resources/images/devguide/architecture/databinding.png)

Angular supports data binding, a mechanism for coordinating parts of a template with parts of a component. Add binding markup to the template HTML to tell Angular how to connect both sides.

```javascript
	<li>{{hero.name}}</li>
	<hero-detail [hero]="selectedHero"></hero-detail>
	<li (click)="selectHero(hero)"></li>
```

The `{{hero.name}} interpolation` displays the component's hero.name property value within the <li> tags.

The `[hero] property binding` passes the value of selectedHero from the parent HeroListComponent to the hero property of the child HeroDetailComponent.

The `(click)` event binding calls the component's selectHero method when the user clicks a hero's name.

**Two-dat data binding** is an important fourth form that combines property and event binding in a single notation, using the *ngModel directive.*

```javascript
<input [(ngModel)]="hero.name">
```

In two-way binding, a data property value flows to the input box from the component as with property binding. But it also work the other way around.

![Data-Binding](https://angular.io/resources/images/devguide/architecture/component-databinding.png)


## Directives

Angular templates are dynamic. When Angular renders them, it transforms the DOM according to the instructions given by directives.
