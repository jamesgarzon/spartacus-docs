---
title: Launch Service
---

The launch service is a Spartacus mechanism that allows the dynamic rendering of non CMS components. This service is intended to be used for components such as dialogs, modals, popovers or other elements of the sort.

## Usage

The launch service supports three launch strategies Outlet, Inline and Routing.

The launch service can be used by invoking the `launch` method in the `LaunchDialogService` with the label of the element to render. The label of the element is specified via configuration.

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

When using a launching a component using the `outlet` or `inline` strategy it is possible to specify a dialog type. Based on the selected type certain CSS classes will be applied to the component. These components will transform the container into the selected type of dialog. All you have left to do is add the content to your component and style it.

### Others

// TODO

### Closing

// TODO

## Configuration

The launch service supports three launch strategies Outlets, Inline and Routing with different configurations. Each of the elements is tied to a "label" that is used to distinguish configuration.

The strandard launch configurations look like this:

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

TODO
