# AngularNestedLayout

This project shows a simple example for a classic header - content - footer layout using Angular. 

I had the following goals when creating this project:
- Header and footer shall always be visible and never resize
- The content area in the middle shall always stretch vertically to use all the remaining vertical space
- Show a scrollbar when the content area exceeds available vertical space
- The HTML body shall never show a scrollbar

This project was generated with [Angular CLI](https://github.com/angular/angular-cli) version 7.3.6.

## Challenges

At first sight the problem sounds rather trivial, especcially with pure HTML and CSS. The whole problem became rather challenging in combination with Angular and routing. There are tons of articles, blogs, stackoverflow questions, and code examples describing the same problem. But none of these examples seemed to fully work for me. 

Overall there are two basic challenges:
* How to stretch the content area if the content is smaller than the available space
* How to show a scrollbar in the content area when the content itself overflows the available vertical space

It seems these days the two common approaches for a nested layout with columns and rows are:
* CSS Grid
* CSS Flexbox

I chose CSS Flex because I already had some experience with it. Angular supports Flex through CSS but also via [Angular flex-layout](https://github.com/angular/flex-layout). Both approaches work fine.

## Key points in the solution

There are a few key points which made the solution work:

### Avoid vertical srollbars at the HTML body

To avoid vertical srollbars at the HTML body I had to disable overflow at the top level styles.css:
```
html, body {
  padding: 0;
  margin: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
}
```
### Make the inner content stretch vertically

A component within a flex box will stretch with 'flex: auto' applied. But this only works when flex layout will be applied to all parent components!

Component hierarchy in this example:
```
body
   app-root
      router-outlet
         app-content
            content-wrapper
               dynamic-content
```

Flex box style must be applied to the router-outlet via the host property in app.component.css:
```
:host {
  display: flex;
  flex-direction: column;
  flex: auto;
  height: 100%;
}
```
Height 100% makes sure that the next child component can stretch vertically. 

File content.component.html contains the actual content (a list of h1 lines) surrounded by a wrapper div element. The wrapper div element makes sure that the content can be scrolled vertically. The content component itself will be loaded into the angular router-outlet. Similar to the router-outlet itself, flex box layout must be applied to the component's host property.

File content.component.css:
```
:host {
  display: flex;
  flex-direction: column;
  flex: auto;
}

.wrapper {
  overflow: auto;
  height: 0;
}
```

The most important setting here is to apply a height to the wrapper div element! Any height including 0 is fine. Without it doesn't work! 

I assume this has to do with the general rule, a scrollbar can only be shown for an element which as a defined height. The actual available height for the content component is automatically derived from the brwoser's current viewport (that was the goal in the first place). Nevertheless, the solution only worked by applying a height property to the wrapper div itself! I also tried it without a wrapper div by applying the same settings directly to the host element, but this didn't work either.

## Development server

Run `ng serve` for a dev server. Navigate to `http://localhost:4200/`. The app will automatically reload if you change any of the source files.

## Code scaffolding

Run `ng generate component component-name` to generate a new component. You can also use `ng generate directive|pipe|service|class|guard|interface|enum|module`.

## Build

Run `ng build` to build the project. The build artifacts will be stored in the `dist/` directory. Use the `--prod` flag for a production build.

## Running unit tests

Run `ng test` to execute the unit tests via [Karma](https://karma-runner.github.io).

## Running end-to-end tests

Run `ng e2e` to execute the end-to-end tests via [Protractor](http://www.protractortest.org/).

## Further help

To get more help on the Angular CLI use `ng help` or go check out the [Angular CLI README](https://github.com/angular/angular-cli/blob/master/README.md).
