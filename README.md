[中文](./StateForm_zh.md)  

# StateForm  

StateForm defines a JSON-based structure which can be used to describe the state of a form. In addition, it defines some form events and built-in components. With the help of the related implementations, you may build a form with a JSON object.

Currently, it is used in [gateschema-form-vue](https://github.com/GateSchema/gateschema-form-vue) and [gateschema-form-react](https://github.com/GateSchema/gateschema-form-react). Just pass a [GateSchema](https://github.com/GateSchema/gateschema-js), and you will get a ready-to-use form with validation.

## State

The state of a form is described by a tree-like JSON object, every node:
- Contains at least one `component` field, which indicates the component used when the current node is rendering a form
- Contains at least a `path` field, which indicates the hierarchical position of the current node
- Contains an optional `children` field, which indicates its children
- Contains some optional fields for UI layout and some components

- Example:

```json  
{
  "component": "Form",
  "path": "/",
  "cols": {
    "label": 0,
    "wrapper": 24
  },
  "children": [
    {
      "component": "Input",
      "path": "/name",
      "value": "foo"
    },
    {
      "component": "Input",
      "path": "/password",
      "type": "password",
      "placeholder": "Your password"
    }
  ],
  "value": {
    "name": "foo"
  }
}

```

The example above generates a form like this:

![example](docs/resources/f-quickstart.png)  


**More examples, see <a href="https://codesandbox.io/s/lx8q1143mz?expanddevtools=1" target="_blank">CodeSandbox</a>.**

## Event

There are only two events in StateForm, `input` and `submit`.

###  `input`  
The `input` event fires when the user enters a new value in an input component, notifying the upper layer of the new input, and telling it to update the state of the form accordingly. The following three parameters should be passed to the external handler function when the event is triggered.
* path: the path property of the component that triggers the event 
* value: new input value
* index: When the value of the component is a list, and an element in the list is deleted, this value is the index of the element in the list.

### `submit`  

Triggered when the user clicks the `submit` button.

## Built-in Components and Components' Properties

The following lists several basic interfaces.

```ts 

//For layout. Using 24-column grid layout system.

type Cols = {
  span: number;
  offset: number;
}

interface FormItemCols {
  item?: Cols | number; // when layout=inline, `item` cols is needed
  label?: Cols | number;
  wrapper?: Cols | number;

  xsItem?: Cols;
  xsLabel?: Cols;
  xsWrapper?: Cols;

  smItem?: Cols;
  smLabel?: Cols;
  smWrapper?: Cols;

  mdItem?: Cols;
  mdLabel?: Cols;
  mdWrapper?: Cols;

  lgItem?: Cols;
  lgLabel?: Cols;
  lgWrapper?: Cols;
}

// Basic component properties

interface FormItem {
  component: string,
  path: string;
  children?: FormItem[]

  hidden?: boolean;
  // css class
  class?: string | {
    [key: string]: boolean
  }

  layout?: "vertical" | "horizontal" | "inline";

  cols?: FormItemCols;

  label?: string;
  required?: boolean;
  placeholder?: string;
  help?: string;
  disabled?: boolean;
  //  value of the current component
  value?: any;
  // error message  
  error?: any;
}

```

### Form  


Form is a special component. StateForm MUST use Form as root component.
A form MUST define its `cols` property. If the descendant component does not define `cols`, it will use the root component's `cols`.
```ts  
interface Form {
  component: "Form";
  path: '/';
  children: FormItem[];
  cols: FormItemCols;
  submitCols?: FormItemCols;
  submitText?: string;
  showSubmit?: boolean;
}
```

### Input  
```ts  
interface Input extends FormItem {
  type?: 'text' | 'password';
  prepend?: string,
  append?: string
}
```
### Textarea  
```ts
interface Textarea extends FormItem{

}
```

### DatePicker  
```ts
interface DatePicker extends FormItem {
  // Specify the data type that returned to the upper layer when inputting.
  valueType?: "iso" | "second" | "millisecond";
  // Specify the format for display
  // Note, the formats of different implementations may differ from each other.
  format?: string;
}
```
### DateTimePicker  
```ts
interface DateTimePicker extends FormItem {
  valueType?: "iso" | "second" | "millisecond";
  format?: string;
}
```
### Upload  
```ts
interface Upload extends FormItem {
  // The display style of the file uploaded
  listType?: "text" | "picture";
  // The text of the upload button
  uploadText?: string;
}
```
### UploadList 
```ts
interface UploadList extends FormItem {
  listType?: "text" | "picture" ;
  uploadText?: string;
}
```
### InputNumber  
```ts
interface InputNumber extends FormItem {
}
```
### Switch  
Used to receive and display the value of `boolean`, such as Open/Close.
```ts
interface Switch extends FormItem {
}
```

### BoolCheck  
Like `Switch`, `BoolCheck` component is also used to receive and display the value of `boolean`, but it uses a `Checkbox` to display.
```ts
interface BoolCheck extends FormItem {
  content?: string
}
```

### Map  
Used to receive input of `Map`-type data, which is like the "nested" form.
```ts
interface Map extends FormItem {
}
```

### List  
```ts
interface List extends FormItem {
  showAddButton?: boolean;
  AddText?: string;
}
```

### Radio, Checkbox, Select  
```ts
type Option =
  | string
  | {
    [key: string]: any;
  };

interface Radio extends FormItem {
  option: Option;
  disabledItems: {
    [key: string]: boolean
  };
}

interface Checkbox extends FormItem {
  option: Option;
  disabledItems: {
    [key: string]: boolean
  };
}

interface Select extends FormItem {
  multiple?: boolean;
  option: Option;
  disabledItems: {
    [key: string]: boolean
  };
}
```

## Example  
<a href="https://codesandbox.io/s/lx8q1143mz?expanddevtools=1" target="_blank">CodeSandbox</a>

## Implementation    
* [stateform-iview](https://github.com/stateform/stateform-iview)  
* [stateform-antd](https://github.com/stateform/stateform-antd)

## License  
Apache 2.0
