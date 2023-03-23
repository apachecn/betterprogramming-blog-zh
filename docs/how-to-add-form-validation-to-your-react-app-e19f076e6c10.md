# 如何向 React 应用程序添加表单验证

> 原文：<https://betterprogramming.pub/how-to-add-form-validation-to-your-react-app-e19f076e6c10>

## 用 Formik、Yep 和 React Bootstrap 构建一个地址簿应用程序

![](img/def6539b386cc94b38cdfa6c991dd03b.png)

由 [timJ](https://unsplash.com/@the_roaming_platypus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/search/photos/address?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

React 是一个简单的库，用于创建交互式前端 web 应用程序。它的特性集是基本的。它为您提供了基于组件的架构来构建 web 应用程序。

每个组件在一个应用程序中做一件小事，它们可以相互嵌套或并排放置。它不附带表单验证，所以构建没有库的复杂表单不会是一种愉快的体验。

如果要创建进行表单验证的表单，需要使用第三方库。

[Formik](https://github.com/jaredpalmer/formik) 和 [Yup](https://github.com/jquense/yup) 配合得非常好，可以满足大多数表单验证需求。Formik 让我们能够构建表单、显示错误并处理表单值的更改，这是另一件我们必须手工完成的事情。

是的，让我们写一个模式来验证我们的表单域。它可以检查几乎任何东西，带有通用的验证码，如电子邮件和必填字段，作为内置功能提供。它还可以根据国家检查依赖于其他字段的字段，如邮政编码格式。

在这一块，我们将构建一个地址簿应用程序，它使用那些库，加上 [React Bootstrap](https://react-bootstrap.github.io/) ，它与上面的库有很大的集成来创建表单。

# 入门指南

首先，我们需要运行 [Create React App](https://github.com/facebook/create-react-app) 来搭建应用。

我们运行`npx create-react-app address-book`来创建包含初始文件的应用项目文件夹。该应用程序将有一个主页来显示联系人，并让我们打开一个模式来添加联系人。

将有一个表，显示所有的联系人和编辑和删除按钮，每一行编辑或删除每个联系人。联系人将存储在一个中央 Redux 存储中，将联系人存储在一个中央位置，使他们易于访问。

[React 路由器](https://reacttraining.com/react-router/)将用于路由。联系人将保存在后端，使用 [JSON 服务器包](https://github.com/typicode/json-server)生成。

一旦完成，我们必须安装一些库。为了安装我们上面提到的库，我们运行`npm i axios bootstrap formik react-bootstrap react-redux react-router-dom yup`。

Axios 是我们用来向后端发出 HTTP 请求的 HTTP 客户端。`react-router-dom`是 React 路由器最新版本的包名。

现在我们已经安装了所有的库，我们可以开始构建应用程序了。所有文件都将在`src`文件夹中，除非另有说明。

首先，我们在 Redux 商店工作。我们在`src`文件夹中创建一个名为`actionCreator.js`的文件，并添加以下内容:

```
import { SET_CONTACTS } from './actions';const setContacts = (contacts) => {
    return {
        type: SET_CONTACTS,
        payload: contacts
    }
};export { setContacts };
```

这是动作创建器，用于创建在商店中存储联系人的动作。

我们创建另一个名为`actions.js`的文件，并添加:

```
const SET_CONTACTS = 'SET_CONTACTS';export { SET_CONTACTS };
```

这具有用于分派动作的类型常数。

在`App.js`中，我们用以下代码替换现有代码:

```
import React from 'react';
import { Router, Route, Link } from "react-router-dom";
import HomePage from './HomePage';
import { createBrowserHistory as createHistory } from 'history'
import Navbar from 'react-bootstrap/Navbar';
import Nav from 'react-bootstrap/Nav';
import './App.css';
const history = createHistory();function App() {
  return (
    <div className="App">
      <Router history={history}>
        <Navbar bg="primary" expand="lg" variant="dark" >
          <Navbar.Brand href="#home">Address Book App</Navbar.Brand>
          <Navbar.Toggle aria-controls="basic-navbar-nav" />
          <Navbar.Collapse id="basic-navbar-nav">
            <Nav className="mr-auto">
              <Nav.Link href="/">Home</Nav.Link>
            </Nav>
          </Navbar.Collapse>
        </Navbar>
        <Route path="/" exact component={HomePage} />
      </Router>
    </div>
  );
}export default App;
```

这是我们添加导航栏并显示 React 路由器路由的路线的地方。在`App.css`中，为了使文本居中，我们将现有代码替换为:

```
.App {
  text-align: center;
}
```

接下来，我们建立我们的联系方式。这是我们应用程序中逻辑最强的部分。我们创建一个名为`ContactForm.js`的文件，并添加:

```
import React from 'react';
import { Formik } from 'formik';
import Form from 'react-bootstrap/Form';
import Col from 'react-bootstrap/Col';
import InputGroup from 'react-bootstrap/InputGroup';
import Button from 'react-bootstrap/Button';
import * as yup from 'yup';
import { COUNTRIES } from './exports';
import PropTypes from 'prop-types';
import { addContact, editContact, getContacts } from './requests';
import { connect } from 'react-redux';
import { setContacts } from './actionCreators';const schema = yup.object({
  firstName: yup.string().required('First name is required'),
  lastName: yup.string().required('Last name is required'),
  address: yup.string().required('Address is required'),
  city: yup.string().required('City is required'),
  region: yup.string().required('Region is required'),
  country: yup.string().required('Country is required').default('Afghanistan'),
  postalCode: yup
    .string()
    .when('country', {
      is: 'United States',
      then: yup.string().matches(/^[0-9]{5}(?:-[0-9]{4})?$/, 'Invalid postal code'),
    })
    .when('country', {
      is: 'Canada',
      then: yup.string().matches(/^[A-Za-z]\d[A-Za-z][ -]?\d[A-Za-z]\d$/, 'Invalid postal code'),
    })
    .required(),
  phone: yup
    .string()
    .when('country', {
      is: country => ["United States", "Canada"].includes(country),
      then: yup.string().matches(/^[2-9]\d{2}[2-9]\d{2}\d{4}$/, 'Invalid phone nunber')
    })
    .required(),
  email: yup.string().email('Invalid email').required('Email is required'),
  age: yup.number()
    .required('Age is required')
    .min(0, 'Minimum age is 0')
    .max(200, 'Maximum age is 200'),
});function ContactForm({
  edit,
  onSave,
  setContacts,
  contact,
  onCancelAdd,
  onCancelEdit,
}) {
  const handleSubmit = async (evt) => {
    const isValid = await schema.validate(evt);
    if (!isValid) {
      return;
    }
    if (!edit) {
      await addContact(evt);
    }
    else {
      await editContact(evt);
    }
    const response = await getContacts();
    setContacts(response.data);
    onSave();
  }return (
    <div className="form">
      <Formik
        validationSchema={schema}
        onSubmit={handleSubmit}
        initialValues={contact || {}}
      >
        {({
          handleSubmit,
          handleChange,
          handleBlur,
          values,
          touched,
          isInvalid,
          errors,
        }) => (
            <Form noValidate onSubmit={handleSubmit}>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="firstName">
                  <Form.Label>First name</Form.Label>
                  <Form.Control
                    type="text"
                    name="firstName"
                    placeholder="First Name"
                    value={values.firstName || ''}
                    onChange={handleChange}
                    isInvalid={touched.firstName && errors.firstName}
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.firstName}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="lastName">
                  <Form.Label>Last name</Form.Label>
                  <Form.Control
                    type="text"
                    name="lastName"
                    placeholder="Last Name"
                    value={values.lastName || ''}
                    onChange={handleChange}
                    isInvalid={touched.firstName && errors.lastName}
                  /><Form.Control.Feedback type="invalid">
                    {errors.lastName}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="address">
                  <Form.Label>Address</Form.Label>
                  <InputGroup>
                    <Form.Control
                      type="text"
                      placeholder="Address"
                      aria-describedby="inputGroupPrepend"
                      name="address"
                      value={values.address || ''}
                      onChange={handleChange}
                      isInvalid={touched.address && errors.address}
                    />
                    <Form.Control.Feedback type="invalid">
                      {errors.address}
                    </Form.Control.Feedback>
                  </InputGroup>
                </Form.Group>
              </Form.Row>
              <Form.Row>
                <Form.Group as={Col} md="12" controlId="city">
                  <Form.Label>City</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="City"
                    name="city"
                    value={values.city || ''}
                    onChange={handleChange}
                    isInvalid={touched.city && errors.city}
                  /><Form.Control.Feedback type="invalid">
                    {errors.city}
                  </Form.Control.Feedback>
                </Form.Group>
                <Form.Group as={Col} md="12" controlId="region">
                  <Form.Label>Region</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="Region"
                    name="region"
                    value={values.region || ''}
                    onChange={handleChange}
                    isInvalid={touched.region && errors.region}
                  />
                  <Form.Control.Feedback type="invalid">
                    {errors.region}
                  </Form.Control.Feedback>
                </Form.Group><Form.Group as={Col} md="12" controlId="country">
                  <Form.Label>Country</Form.Label>
                  <Form.Control
                    as="select"
                    placeholder="Country"
                    name="country"
                    onChange={handleChange}
                    value={values.country || ''}
                    isInvalid={touched.region && errors.country}>
                    {COUNTRIES.map(c => <option key={c} value={c}>{c}</option>)}
                  </Form.Control>
                  <Form.Control.Feedback type="invalid">
                    {errors.country}
                  </Form.Control.Feedback>
                </Form.Group><Form.Group as={Col} md="12" controlId="postalCode">
                  <Form.Label>Postal Code</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="Postal Code"
                    name="postalCode"
                    value={values.postalCode || ''}
                    onChange={handleChange}
                    isInvalid={touched.postalCode && errors.postalCode}
                  /><Form.Control.Feedback type="invalid">
                    {errors.postalCode}
                  </Form.Control.Feedback>
                </Form.Group><Form.Group as={Col} md="12" controlId="phone">
                  <Form.Label>Phone</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="Phone"
                    name="phone"
                    value={values.phone || ''}
                    onChange={handleChange}
                    isInvalid={touched.phone && errors.phone}
                  /><Form.Control.Feedback type="invalid">
                    {errors.phone}
                  </Form.Control.Feedback>
                </Form.Group><Form.Group as={Col} md="12" controlId="email">
                  <Form.Label>Email</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="Email"
                    name="email"
                    value={values.email || ''}
                    onChange={handleChange}
                    isInvalid={touched.email && errors.email}
                  /><Form.Control.Feedback type="invalid">
                    {errors.email}
                  </Form.Control.Feedback>
                </Form.Group><Form.Group as={Col} md="12" controlId="age">
                  <Form.Label>Age</Form.Label>
                  <Form.Control
                    type="text"
                    placeholder="Age"
                    name="age"
                    value={values.age || ''}
                    onChange={handleChange}
                    isInvalid={touched.age && errors.age}
                  /><Form.Control.Feedback type="invalid">
                    {errors.age}
                  </Form.Control.Feedback>
                </Form.Group>
              </Form.Row>
              <Button type="submit" style={{ 'marginRight': '10px' }}>Save</Button>
              <Button type="button" onClick={edit ? onCancelEdit : onCancelAdd}>Cancel</Button>
            </Form>
          )}
      </Formik>
    </div>
  );
}ContactForm.propTypes = {
  edit: PropTypes.bool,
  onSave: PropTypes.func,
  onCancelAdd: PropTypes.func,
  onCancelEdit: PropTypes.func,
  contact: PropTypes.object
}const mapStateToProps = state => {
  return {
    contacts: state.contacts,
  }
}const mapDispatchToProps = dispatch => ({
  setContacts: contacts => dispatch(setContacts(contacts))
})export default connect(
  mapStateToProps,
  mapDispatchToProps
)(ContactForm);
```

我们在这里使用 Formik，以便于构建我们的联系表单，我们的 Boostrap `Form`组件嵌套在`Formik`组件中，因此我们可以使用 Formik 的`handleChange`、`handleSubmit`、`values`、`touched`和`errors`参数。

`handleChange`是一个函数，让我们不用自己写代码就可以从输入中更新表单域数据。`handleSubmit`是我们传递给`Formik`组件的`onSubmit`处理程序的函数。

函数中的参数是我们输入的数据，字段名作为键，由每个字段的`name`属性定义，每个字段的值作为那些键的值。

注意，在每个`value`属性中，我们有`||''`，这样我们就不会得到未定义的值，并防止不受控制的表单警告被触发。

为了显示表单验证消息，我们必须将`isInvalid`属性传递给每个`Form.Control`组件。`schema`对象是`Formik`将检查表单验证的对象。

`required`函数中的参数是验证错误消息。`matches`、`min`和`max`函数的第二个参数也是验证消息。

`ContactForm`函数的参数是 props，我们将从稍后构建的`HomePage`组件传入。

`handleSubmit`功能检查数据是否有效，如果有效，将根据是添加还是编辑联系人进行保存。

保存成功后，我们在存储中设置联系人并调用`onSave` prop，这是一个关闭表单所在模态的函数。模式将在主页上定义。

`mapStateToProps`是 React Redux 提供的一个函数，因此我们可以将状态直接映射到组件的道具，正如函数名所示。

`mapDispatchToProps`允许我们在组件的 props 中调用一个名为`setContacts`的函数来调度动作，正如我们在`actionCreators.js`中定义的那样。

接下来，我们创建一个名为`exports.js`的文件，并将:

```
export const COUNTRIES = ["Afghanistan", "Albania", "Algeria", "Andorra", "Angola", "Anguilla", "Antigua &amp; Barbuda", "Argentina", "Armenia", "Aruba", "Australia", "Austria", "Azerbaijan", "Bahamas"
    , "Bahrain", "Bangladesh", "Barbados", "Belarus", "Belgium", "Belize", "Benin", "Bermuda", "Bhutan", "Bolivia", "Bosnia &amp; Herzegovina", "Botswana", "Brazil", "British Virgin Islands"
    , "Brunei", "Bulgaria", "Burkina Faso", "Burundi", "Cambodia", "Cameroon", "Canada", "Cape Verde", "Cayman Islands", "Chad", "Chile", "China", "Colombia", "Congo", "Cook Islands", "Costa Rica"
    , "Cote D Ivoire", "Croatia", "Cruise Ship", "Cuba", "Cyprus", "Czech Republic", "Denmark", "Djibouti", "Dominica", "Dominican Republic", "Ecuador", "Egypt", "El Salvador", "Equatorial Guinea"
    , "Estonia", "Ethiopia", "Falkland Islands", "Faroe Islands", "Fiji", "Finland", "France", "French Polynesia", "French West Indies", "Gabon", "Gambia", "Georgia", "Germany", "Ghana"
    , "Gibraltar", "Greece", "Greenland", "Grenada", "Guam", "Guatemala", "Guernsey", "Guinea", "Guinea Bissau", "Guyana", "Haiti", "Honduras", "Hong Kong", "Hungary", "Iceland", "India"
    , "Indonesia", "Iran", "Iraq", "Ireland", "Isle of Man", "Israel", "Italy", "Jamaica", "Japan", "Jersey", "Jordan", "Kazakhstan", "Kenya", "Kuwait", "Kyrgyz Republic", "Laos", "Latvia"
    , "Lebanon", "Lesotho", "Liberia", "Libya", "Liechtenstein", "Lithuania", "Luxembourg", "Macau", "Macedonia", "Madagascar", "Malawi", "Malaysia", "Maldives", "Mali", "Malta", "Mauritania"
    , "Mauritius", "Mexico", "Moldova", "Monaco", "Mongolia", "Montenegro", "Montserrat", "Morocco", "Mozambique", "Namibia", "Nepal", "Netherlands", "Netherlands Antilles", "New Caledonia"
    , "New Zealand", "Nicaragua", "Niger", "Nigeria", "Norway", "Oman", "Pakistan", "Palestine", "Panama", "Papua New Guinea", "Paraguay", "Peru", "Philippines", "Poland", "Portugal"
    , "Puerto Rico", "Qatar", "Reunion", "Romania", "Russia", "Rwanda", "Saint Pierre &amp; Miquelon", "Samoa", "San Marino", "Satellite", "Saudi Arabia", "Senegal", "Serbia", "Seychelles"
    , "Sierra Leone", "Singapore", "Slovakia", "Slovenia", "South Africa", "South Korea", "Spain", "Sri Lanka", "St Kitts &amp; Nevis", "St Lucia", "St Vincent", "St. Lucia", "Sudan"
    , "Suriname", "Swaziland", "Sweden", "Switzerland", "Syria", "Taiwan", "Tajikistan", "Tanzania", "Thailand", "Timor L'Este", "Togo", "Tonga", "Trinidad &amp; Tobago", "Tunisia"
    , "Turkey", "Turkmenistan", "Turks &amp; Caicos", "Uganda", "Ukraine", "United Arab Emirates", "United Kingdom", "United States", "United States Minor Outlying Islands", "Uruguay"
    , "Uzbekistan", "Venezuela", "Vietnam", "Virgin Islands (US)", "Yemen", "Zambia", "Zimbabwe"];
```

这些是表单中国家字段的国家。

在`HomePage.js`中，我们把:

```
import React from 'react';
import { useState, useEffect } from 'react';
import Table from 'react-bootstrap/Table'
import ButtonToolbar from 'react-bootstrap/ButtonToolbar';
import Button from 'react-bootstrap/Button';
import Modal from 'react-bootstrap/Modal'
import ContactForm from './ContactForm';
import './HomePage.css';
import { connect } from 'react-redux';
import { getContacts, deleteContact } from './requests';function HomePage() {
  const [openAddModal, setOpenAddModal] = useState(false);
  const [openEditModal, setOpenEditModal] = useState(false);
  const [initialized, setInitialized] = useState(false);
  const [selectedId, setSelectedId] = useState(0);
  const [selectedContact, setSelectedContact] = useState({});
  const [contacts, setContacts] = useState([]);const openModal = () => {
    setOpenAddModal(true);
  }const closeModal = () => {
    setOpenAddModal(false);
    setOpenEditModal(false);
    getData();
  }const cancelAddModal = () => {
    setOpenAddModal(false);
  }const editContact = (contact) => {
    setSelectedContact(contact);
    setOpenEditModal(true);
  }const cancelEditModal = () => {
    setOpenEditModal(false);
  }const getData = async () => {
    const response = await getContacts();
    setContacts(response.data);
    setInitialized(true);
  }const deleteSelectedContact = async (id) => {
    await deleteContact(id);
    getData();
  }useEffect(() => {
    if (!initialized) {
      getData();
    }
  })return (
    <div className="home-page">
      <h1>Contacts</h1>
      <Modal show={openAddModal} onHide={closeModal} >
        <Modal.Header closeButton>
          <Modal.Title>Add Contact</Modal.Title>
        </Modal.Header>
        <Modal.Body>
          <ContactForm edit={false} onSave={closeModal.bind(this)} onCancelAdd={cancelAddModal} />
        </Modal.Body>
      </Modal><Modal show={openEditModal} onHide={closeModal}>
        <Modal.Header closeButton>
          <Modal.Title>Edit Contact</Modal.Title>
        </Modal.Header>
        <Modal.Body>
          <ContactForm edit={true} onSave={closeModal.bind(this)} contact={selectedContact} onCancelEdit={cancelEditModal} />
        </Modal.Body>
      </Modal>
      <ButtonToolbar onClick={openModal}>
        <Button variant="outline-primary">Add Contact</Button>
      </ButtonToolbar>
      <br />
      <Table striped bordered hover>
        <thead>
          <tr>
            <th>First Name</th>
            <th>Last Name</th>
            <th>Address</th>
            <th>City</th>
            <th>Country</th>
            <th>Postal Code</th>
            <th>Phone</th>
            <th>Email</th>
            <th>Age</th>
            <th>Edit</th>
            <th>Delete</th>
          </tr>
        </thead>
        <tbody>
          {contacts.map(c => (
            <tr key={c.id}>
              <td>{c.firstName}</td>
              <td>{c.lastName}</td>
              <td>{c.address}</td>
              <td>{c.city}</td>
              <td>{c.country}</td>
              <td>{c.postalCode}</td>
              <td>{c.phone}</td>
              <td>{c.email}</td>
              <td>{c.age}</td>
              <td>
                <Button variant="outline-primary" onClick={editContact.bind(this, c)}>Edit</Button>
              </td>
              <td>
                <Button variant="outline-primary" onClick={deleteSelectedContact.bind(this, c.id)}>Delete</Button>
              </td>
            </tr>
          ))}
        </tbody>
      </Table>
    </div>
  );
}const mapStateToProps = state => {
  return {
    contacts: state.contacts,
  }
}export default connect(
  mapStateToProps,
  null
)(HomePage);
```

它有显示联系人的表格和添加、编辑和删除联系人的按钮。它在第一次加载时用`useEffect`的回调函数中的`getData`函数调用获取一次数据。

每次渲染都会调用`useEffect`的回调，所以我们想设置一个`initialized`标志，并检查它是否只在`true`时才加载。

注意，我们将这个组件中的所有道具传递给了`ContactForm`组件。

要将一个参数传递给一个`onClick`处理函数，我们必须调用函数上的`bind`，并将函数的参数作为第二个参数传递给`bind`。

例如，在这个文件中，我们有`editContact.bind(this, c)`，其中`c`是联系对象。`editContact`功能定义如下:

```
const editContact = (contact) => {
    setSelectedContact(contact);
    setOpenEditModal(true);
  }
```

`c`是我们传入的`contact`参数。

接下来，我们创建一个名为`HomePage.css`的文件，并将:

```
.home-page {
  padding: 20px;
}
```

添加一些填充。

在`index.js`中，我们将现有代码替换为:

```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';
import { contactsReducer } from './reducers';
import { Provider } from 'react-redux'
import { createStore, combineReducers } from 'redux'const addressBookApp = combineReducers({
    contacts: contactsReducer,
})const store = createStore(addressBookApp)ReactDOM.render(
    <Provider store={store}>
        <App />
    </Provider>
    , document.getElementById('root'));// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: [https://bit.ly/CRA-PWA](https://bit.ly/CRA-PWA)
serviceWorker.unregister();
```

我们组合了 reducers 并创建了 store，然后用`Provider`组件将它注入到我们的应用程序中，这样我们就可以在应用程序的任何地方使用它。

然后，我们创建一个名为`reducers.js`的文件，并添加:

```
import { SET_CONTACTS } from './actions';function contactsReducer(state = {}, action) {
    switch (action.type) {
        case SET_CONTACTS:
            state = JSON.parse(JSON.stringify(action.payload));
            return state;
        default:
            return state
    }
}export { contactsReducer };
```

这是我们存储联系人的缩减器，我们通过调用组件中的`mapDispatchToProps`函数提供的 prop 来调度这些联系人。

我们创建一个名为`requests.js`的文件，并添加:

```
const APIURL = '[http://localhost:3000'](http://localhost:3000');
const axios = require('axios');
export const getContacts = () => axios.get(`${APIURL}/contacts`);
export const addContact = (data) => axios.post(`${APIURL}/contacts`, data);
export const editContact = (data) => axios.put(`${APIURL}/contacts/${data.id}`, data);
export const deleteContact = (id) => axios.delete(`${APIURL}/contacts/${id}`);
```

这些功能向后端发出 HTTP 请求，以保存和删除联系人。

最后，在`public/index.html`中，我们将现有代码替换为:

```
<!DOCTYPE html>
<html lang="en"><head>
  <meta charset="utf-8" />
  <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="theme-color" content="#000000" />
  <meta name="description" content="Web site created using create-react-app" />
  <link rel="apple-touch-icon" href="logo192.png" />
  <link rel="manifest" crossorigin="use-credentials" href="%PUBLIC_URL%/manifest.json" /><!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See [https://developers.google.com/web/fundamentals/web-app-manifest/](https://developers.google.com/web/fundamentals/web-app-manifest/)
    -->
  <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
  <title>React Address Book App</title>
  <link rel="stylesheet" href="[https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css](https://maxcdn.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css)"
    integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous" />
</head><body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
</body></html>
```

更改标题并添加引导样式表。

现在，我们可以通过在 Windows 上运行`set PORT=3001 && react-scripts start`或在 Linux 上运行`PORT=3006 react-scripts start`来运行应用程序。

为了启动后端，我们首先通过运行`npm i json-server`来安装`json-server`包。然后，转到我们的项目文件夹并运行:

```
json-server --watch db.json
```

在`db.json`中，将文本改为:

```
{
  "contacts": [
  ]
}
```

所以我们有了在可用的`requests.js`中定义的`contacts`端点。

最终，我们得到了以下结果:

![](img/f0189119955e2e23d8ca9a28cc9761f9.png)![](img/751d2681035b39a9bbd8da8b261ec55b.png)![](img/9d3348846e2374867d8616963dcd66e0.png)