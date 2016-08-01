# Simple React Form

[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com/)
[![Codewake](https://www.codewake.com/badges/ask_question.svg)](https://www.codewake.com/p/simple-react-form)

Simple React Form is a framework that simplifies the use of forms in React and React Native

This is just a framework, you must [create the form components](https://github.com/nicolaslopezj/simple-react-form/blob/master/docs/create-input-types.md) that you will use.

If you use material-ui you are lucky, because I published a material-ui set of components.
[simple-react-form-material-ui](https://github.com/nicolaslopezj/simple-react-form-material-ui).

Made for Meteor, but works without Meteor too. This package was inspired by aldeed's autoform.

### Installation

Install the base package

```sh
npm install --save simple-react-form
```

If you use material-ui install that package too

```sh
npm install --save simple-react-form-material-ui
```

And register the material-ui components, just once in your app.

```js
import 'simple-react-form-material-ui'
```

Go to the [docs](https://github.com/nicolaslopezj/simple-react-form/tree/master/docs) folder to continue.

Browse the [examples](https://github.com/nicolaslopezj/simple-react-form-examples).

### Example

```jsx
import React from 'react'
import {Form, Field} from 'simple-react-form'

class PostsCreate extends React.Component {

  constructor(props) {
    super(props)
    this.state = {}
  }

  render() {
    return (
      <Form
        state={this.state}
        onChange={changes => this.setState(changes)}>
        <Field fieldName='title' type='string' label='Title'/>
        <Field fieldName='body' type='textarea' label='Body'/>
        <p>
          The title is "{this.state.title}"
        </p>
      </Form>
    )
  }
}
```

You can find more examples [here](https://github.com/nicolaslopezj/simple-react-form-examples).

# Docs

## Preparation

*You must add this lines of code in your app just once.*

##### Register the fields

```js
import 'simple-react-form-material-ui'
```


## Using with state

An insert form.

```jsx
import React from 'react'
import {Form, Field} from 'simple-react-form'

class PostsCreate extends React.Component {

  constructor(props) {
    super(props)
    this.state = {}
  }

  render() {
    return (
      <Form
        state={this.state}
        onChange={changes => this.setState(changes)}>
        <Field fieldName='title' type='string' label='Title'/>
        <Field fieldName='body' type='textarea' label='Body'/>
        <p>
          The title is "{this.state.title}"
        </p>
      </Form>
    )
  }
}
```

## Use with Meteor Simple Schema

Automatic forms creation with [aldeed:simple-schema](http://github.com/aldeed/simple-schema) and React.

##### Allow ```srf``` field for schemas

With simple-schema you must define the object attributes that are not the basics.

Just add this code once in your app.

```js
SimpleSchema.extendOptions({
  srf: Match.Optional(Object)
})
```

### Basic Example

Schema

```js
Posts = new Meteor.Collection('posts')

Posts.attachSchema({
  title: {
    type: String,
  },
  body: {
    type: String,
    label: 'Content',
    mrf: {
      type: 'textarea',
    },
  }
})
```

An insert form.

```jsx
import React from 'react'
import { Form } from 'simple-react-form'
import Posts from '../../collections/posts'

class PostsCreate extends React.Component {
  render() {
    return (
      <div>
        <h1>Create a post</h1>
        <Form
          collection={Posts}
          type='insert'
          ref='form'
          onSuccess={(docId) => FlowRouter.go('posts.update', { postId: docId })}
          />
        <RaisedButton label='Create' onTouchTap={() => this.refs.form.submit()}/>
      </div>
    )
  },
}
```

An update form.

```jsx
import React from 'react'
import { Form, Field } from 'simple-react-form'
import Posts from '../../collections/posts'

class PostsUpdate extends React.Component {
  render() {
    return (
      <div>
        <h1>Post update</h1>
        <Form
          collection={Posts}
          type='update'
          ref='form'
          doc={this.props.post}>
          <Field fieldName='title'/>
          <Field fieldName='body'/>
        </Form>
        <RaisedButton primary={true} label='Save' onTouchTap={() => this.refs.form.submit()}/>
      </div>
    )
  }
}
```

## Create Input Types

React Simple Form is built from the idea that you can create custom components easily.

Basically this consist in a component that have the prop ```value``` and the prop ```onChange```.
You must render the ```value``` and call ```onChange``` passing the new value
when the value has changed.

You can also pass props to this components setting them in the srf parameter of
the simple-schema object:

```
Post.attachSchema({
  picture: {
    type: String,
    srf: {
      type: 'my-upload-picture',
      squareOnly: true
    }
  }
})
```

Or simply in the field while rendering:

```jsx
<Field fieldName='picture' type='my-upload-picuture' squareOnly={true}/>
```

### Creating field types

You must create a React component that extends ```FieldType```
and register the field type

```jsx
import {FieldType, registerType} from 'simple-react-form'

class MyUploadPicture extends FieldType {
  render() {
    return (
      <div>
        <p>
          {this.props.label}
        </p>
        <img src={this.props.value} />
        <TextField
        value={this.props.value}
        hintText='Image Url'
        onChange={(event) => this.props.onChange(event.target.value)} />
        <p>
          {this.props.errorMessage}
        </p>
      </div>  
    );
  }
}

registerType({
  type: 'textarea',
  component: MyUploadPicture
})
```

You can view the full list of props [here](https://github.com/nicolaslopezj/simple-react-form/blob/master/src/field.jsx#L11).

## React Native

With React Native the api is the same, but you must pass the option ```useFormTag={false}``` to the form and register all the types you want to use.

Example:

Register the field types. Register the fields once in your app.

```js
import React from 'react'
import {FieldType, registerType} from 'simple-react-form'
import {View, TextInput} from 'react-native'

const propTypes = {
  ...FieldType.propTypes
}

const defaultProps = {
  ...FieldType.defaultProps
}

class TextFieldComponent extends FieldType {

  render () {
    return (
      <View>
        <TextInput
        style={{height: 40, borderColor: 'gray', borderWidth: 1}}
        onChangeText={this.props.onChange}
        value={this.props.value}/>
      </View>
    )
  }
}

TextFieldComponent.propTypes = propTypes
TextFieldComponent.defaultProps = defaultProps

registerType({
  type: 'text',
  component: TextFieldComponent
})
```

Render the form in the component you want

```js
<Form state={this.state} onChange={changes => this.setState(changes)} useFormTag={false}>
  <Field fieldName='name' type='text'/>
</Form>
```
