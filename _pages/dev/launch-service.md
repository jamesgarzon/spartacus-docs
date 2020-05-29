---
title: Launch Service (DRAFT)
---

The launch service is a Spartacus mechanism that allows the dynamic rendering of non CMS components. This service is intended to be used for components such as dialogs, modals, popovers or other elements of the sort.

## Usage

The launch service supports three launch strategies Outlet, Inline and Routing. Each strategy extends a common base called the `LaunchRenderStrategy`.

The launch service can be used by invoking the `launch` method in the `LaunchDialogService` with the label of the element to render. The label of the element is specified via configuration. Using the configuration label, the `LaunchRenderStrategy` is able to delegate the rendering of the component to the correct strategy.

The following diagram explains the architecture of the system.

![launch service architecture]({{ site.baseurl }}/assets/images/launch-service-architecture.png)

The `launch` method will return `void` for the routing strategy and `Observable<ComponentRef<any>>` for the `inline` and `outlet` strategy. This observable can be used to handle focus or to interact with the rendered component.

### Outlet

The `outlet` strategy allows the dynamic rendering of an element within a specified outlet.

### Inline

The `inline` strategy allows the dynamic rendering of an element in the DOM following the component that triggered the rendering.

For example:

```html
<!-- Trigger Element -->
<trigger-element>[...]</trigger-element>
<!-- Rendered Element -->
<rendered-element>[...]</rendered-element>
```

### Routing

The `routing` strategy will navigate to the configured route.

### Creating the component

#### Dialog

When using the launch mechanism for a component with the `outlet` or `inline` strategy it is possible to specify a dialog type. Based on the selected type certain CSS classes will be applied to the component. These components will transform the container into the selected type of dialog. All you have left to do is add the content to your component and style it.

### Others

By omitting the `dialogType` configuration in your component's configuration, the component you are launching will not be styled. This is useful if your component does not fit one of the default types.

### Closing

These are the steps to close a component opened with the launch service.

1. Destroy the component using the `destroy()` method in the component reference returned from the `LaunchDialogService.launch`.
2. Call the `clear()` method in the `LaunchDialogService` to clear the component from the list of launched components.

## Configuration

The launch service supports three launch strategies Outlets, Inline and Routing with different configurations. Each of the elements is tied to a "label" that is used to distinguish configuration.

The standard launch configurations look like this:

```ts
launch: {
  [label: string]: LaunchOptions
}

// The Launch Options type is as follows
type LaunchOptions =
  | LaunchOutletDialog
  | LaunchInlineDialog
  | LaunchRoute;
```

### Outlets

The available configuration fields for the outlet strategy are:

```ts
interface LaunchOutletDialog {
  /*
   * The class of the component to render
   */
  component: any;
  /*
   * The name of the outlet to render the component
   */
  outlet: string;
  /*
   * The position for the rendering (default: OutletPosition.BEFORE)
   */
  position?: OutletPosition;
  /*
   * Can the element be rendered multiple times simultaneously
   */
  multi?: string;
  /*
   * The dialog type to apply CSS classes
   */
  dialogType?: DIALOG_TYPE;
}
```

The OutletPosition is the same type as used by the outelts mechanism.

```ts
enum OutletPosition {
  REPLACE = 'replace',
  BEFORE = 'before',
  AFTER = 'after',
}
```

The DIALOG_TYPE is defined as follows.

```ts
enum DIALOG_TYPE {
  POPOVER = 'POPOVER',
  DIALOG = 'DIALOG',
  SIDEBAR_START = 'SIDEBAR_START',
  SIDE
```

### Inline

The available configuration fields for the inline strategy are:

```ts
interface LaunchInlineDialog {
  inline: boolean;
  /*
   * The class of the component to render
   */
  component: any;
  /*
   * Can the element be rendered multiple times simultaneously
   */
  multi?: string;
  /*
   * The dialog type to apply CSS classes
   */
  dialogType?: DIALOG_TYPE;
}
```

### Routing

The available configuration fields for the routing strategy are:

```ts
interface LaunchRoute {
  /**
   * The route for the url
   */
  cxRoute: string;
  /**
   * The parameters for the route
   */
  params?: { [param: string]: any };
}
```

## Customizing the mechanism

### Overriding configurations

The default Spartacus storefront already uses the launch mechanisms to render dialogs and other components. A default configuration for these components is provided in Spartacus.

These configurations can be overridden as follows:

In your `app.module.ts` add the new configuration for the label you wish to override.

```ts
launch: {
  [label: string]: {
    // New configuration
  }
}
```

For example to make the anonymous consent dialog a sidebar you can do:

```ts
launch: {
    ANONYMOUS_CONSENT: {
      outlet: 'cx-storefront',
      inline: false, // Inline false is required when overriding a default set to true
      component: AnonymousConsentDialogComponent,
      dialogType: DIALOG_TYPE.SIDEBAR_START,
    },
  }
```

### Extending the strategies

Like mentioned above, the rendering is handled by three strategies. You can extend the strategies in if you want to customize the way they behave.

Since the strategies are services, you can use standard Angular [dependency injection](https://angular.io/guide/dependency-injection) to override them.

A strategy can be extended as follows:

1. Create a new file with the class that extends the desired strategy

```ts
@Injectable({ providedIn: 'root' })
export class CustomLaunchStrategy extends InlineRenderStrategy {
  render(
    config: LaunchInlineDialog,
    caller: LAUNCH_CALLER | string,
    vcr: ViewContainerRef
  ): Observable<ComponentRef<any>> {
    return super
      .render(config, caller, vcr)
      .pipe(tap((component) => component.onDestroy(() => alert('TADA!!! 🎉'))));
  }
}
```

2. Inject the new service in your `app.module.ts`

```ts
{ provide: InlineRenderStrategy, useClass: CustomLaunchStrategy }
```

The result will render an `alert` when closing the anonymous consent management dialog.

### Styling changes

#### Replacing default classes

You can use the mechanism described above to change the list of CSS classes that will be added to the container.

The strategies contain four arrays of CSS classes mapped to each dialog type, `dialogClasses`, `popoverClasses`, `sidebarEndClasses` and `sidebarStartClasses`.

Each of these arrays can be overridden when extending a strategy.

```ts
@Injectable({ providedIn: 'root' })
export class CustomLaunchStrategy extends InlineRenderStrategy {
  protected dialogClasses = ['custom-dialog-class'];
}
```

#### Modifying default classes

Alternatively, you can modify the default classes provided in the strategies. The default classes can be found in the `launch-render.strategy.ts` file.

These are the steps to follow to modify the default styling classes:

1. Select the class to extend.
2. Create a new class with the same name in your global style (`styles.scss`).
3. Extend the default class `@extend CLASS_NAME;`.
4. Add your own style.

```scss
.cx-dialog-popover {
  @extends .cx-dialog-popover;

  // Override
  color: red;
  height: 50%;
}
```
