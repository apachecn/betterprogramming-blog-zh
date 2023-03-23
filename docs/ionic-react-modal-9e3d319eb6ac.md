# 如何用 Ionic 和 React 建立一个模型

> 原文：<https://betterprogramming.pub/ionic-react-modal-9e3d319eb6ac>

## 如何声明并向 Ionic + React 模式传递参数

![](img/4d6d0795cd8554762e2d54cae8b2b778.png)

照片由 [timJ](https://unsplash.com/@the_roaming_platypus?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/dialog?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄

这些天我对 Ionic React 很感兴趣，因此，我在尝试不同的组件。

其中之一是模态，我几乎毫无例外地在所有应用程序中使用它。

尽管它的专用[文档](https://ionicframework.com/docs/api/modal)非常简洁，但我走得更远了，因为我喜欢在它们自己独立的组件中声明它们。这就是我写这篇新博文的原因。

# 入门指南

为了给应用程序添加一个模型，我们按照文档中显示的那样进行(告诉你，这是有据可查的)。

我们使用组件`IonModal`，为了触发它的打开和关闭，我们还使用一个状态(在一个`useState`钩子的帮助下)来修改它的属性`isOpen`。

```
import React, { useState } from 'react';
import { IonModal, IonButton, IonContent} from '@ionic/react';

export const Tab1: React.FC = () => {
  const [showModal, setShowModal] = useState(false);

  return (
    <IonContent>
      <IonModal isOpen={showModal}>
        <p>This is the modal content.</p>
        <IonButton onClick={() => setShowModal(false)}>
            Close Modal
        </IonButton>
      </IonModal>
      <IonButton onClick={() => setShowModal(true)}>
            Show Modal
      </IonButton>
    </IonContent>
  );
};

export default Tab1;
```

请注意，我使用了`tab`初学者工具包来开发这篇文章，这就是为什么上面的页面的名称是`Tab1`。

# 创建一个组件

模态可能很快变得像页面一样复杂。这就是为什么我习惯于在它们自己的组件中声明它们。然后，让我们试着在一个单独的新文件中创建一个新的，比如叫做`MyModal.tsx`。

```
import React from 'react';
import {IonHeader, IonContent, IonToolbar, IonTitle} from '@ionic/react';

class MyModal extends React.Component {

  render() {
    return <>
      <IonHeader>
        <IonToolbar color="primary">
          <IonTitle>My Modal</IonTitle>
        </IonToolbar>
      </IonHeader>
      <IonContent className="ion-padding">
        <p>This is the modal content.</p>
      </IonContent>
    </>
  };

}

export default MyModal;
```

一旦我们创建了它，我们就可以在我们的页面上使用它来替换以前的模态内容。

```
import React, { useState } from 'react';
import { IonModal, IonButton, IonContent} from '@ionic/react';import MyModal from './MyModal';

export const Tab1: React.FC = () => {
  const [showModal, setShowModal] = useState(false);

  return (
    <IonContent>
      <IonModal isOpen={showModal}>
        <MyModal></MyModal>
        <IonButton onClick={() => setShowModal(false)}>
            Close Modal
        </IonButton>
      </IonModal>
      <IonButton onClick={() => setShowModal(true)}>
            Show Modal
      </IonButton>
    </IonContent>
  );
};

export default Tab1;
```

# 要关闭模态

超级，我们实现了第一步，我们现在有一个在单独的组件中声明的模态。

但是，在我们上面的例子中，分别关闭模态按钮和将显示状态设置为`false`的`IonButton`按钮的动作仍然在我们的组件之外呈现。

从设计的角度来看，这有点不幸，因为我认为在模型本身的头部呈现这样的动作是很常见的。

为了将这个按钮移到模态中，我实际上找到了两种可能的解决方案。一个用的是`callback`，可能是最干净的一个，另一个用的是`references`。

可能会有更多，我真的很高兴听到他们。

# 回收

在这个解决方案中，我们希望向组件传递一个回调来关闭模态。我们用一个新的属性来增强它，我们也在我们的头中使用它来添加相关的按钮。

```
import React from 'react';
import {IonHeader, IonContent, IonToolbar, IonTitle, IonButtons, IonButton, IonIcon} from '@ionic/react';

type MyModalProps = {
  closeAction: Function;
}

class MyModal extends React.Component<MyModalProps> {

  render() {
    return <>
      <IonHeader>
        <IonToolbar color="primary">
          <IonTitle>My Modal</IonTitle>
          <IonButtons slot="end">
            <IonButton onClick={() => this.props.closeAction()}>
              <IonIcon name="close" slot="icon-only"></IonIcon>
            </IonButton>
          </IonButtons>
        </IonToolbar>
      </IonHeader>
      <IonContent className="ion-padding">
        <p>This is the modal content.</p>
      </IonContent>
    </>
  };

}

export default ({closeAction}: { closeAction: Function }) => (
  <MyModal closeAction={closeAction}>
  </MyModal>
)
```

一旦组件被修改，我们可以创建一个新的函数(在我们的页面上)来设置显示状态为`false`，并将其作为回调传递给我们的组件。

```
import React, {useState} from 'react';
import {IonModal, IonButton, IonContent} from '@ionic/react';
import MyModal from './MyModal';

export const Tab1: React.FC = () => {
  const [showModal, setShowModal] = useState(false);

  async function closeModal() {
    await setShowModal(false);
  }

  return (
    <IonContent>
      <IonModal isOpen={showModal}>
        <MyModal closeAction={closeModal}></MyModal>
      </IonModal>
      <IonButton onClick={() => setShowModal(true)}>
        Show Modal
      </IonButton>
    </IonContent>
  );
};

export default Tab1;
```

# 参考

另一个可能的解决方案是使用 DOM 引用来消除模态。

```
import React, {RefObject} from 'react';
import {IonHeader, IonContent, IonToolbar, IonTitle, IonButtons, IonButton, IonIcon} from '@ionic/react';

class MyModal extends React.Component {

  headerRef: RefObject<HTMLIonHeaderElement> = React.createRef();

  async closeModal() {
    if (!this.headerRef || !this.headerRef.current) {
      return;
    }

    await (this.headerRef.current.closest('ion-modal') as 
                HTMLIonModalElement).dismiss();
  }

  render() {
    return <>
      <IonHeader ref={this.headerRef}>
        <IonToolbar color="primary">
          <IonTitle>My Modal</IonTitle>
          <IonButtons slot="end">
            <IonButton onClick={() => this.closeModal()}>
              <IonIcon name="close" slot="icon-only"></IonIcon>
            </IonButton>
          </IonButtons>
        </IonToolbar>
      </IonHeader>
      <IonContent className="ion-padding">
        <p>This is the modal content 3.</p>
      </IonContent>
    </>
  };

}

export default MyModal;
```

上面的方法有一个效果，我们的页面上用于显示目的的`state`，可能最终不再与模态的有效状态同步，因为我们使用 DOM 关闭了它。

为了克服这种情况，我们可以在对话框关闭后同步信息。

```
import React, {useState} from 'react';
import {IonModal, IonButton, IonContent} from '@ionic/react';
import MyModal from './MyModal';

export const Tab1: React.FC = () => {
  const [showModal, setShowModal] = useState(false);

  return (
    <IonContent>
      <IonModal isOpen={showModal}
        onDidDismiss={() => setShowModal(false)}>
        <MyModal></MyModal>
      </IonModal>
      <IonButton onClick={() => setShowModal(true)}>
        Show Modal
      </IonButton>
    </IonContent>
  );
};

export default Tab1;
```

但不幸的是，这种方法有一个缺点。

当我们修改状态以同步它时，我们的组件将被“重新呈现”。因此，它比使用`callback`的解决方案性能稍差，这就是为什么我发现了第一个解决方案清洁剂。

附注:昨天晚上，我花了几个小时尝试将`shouldComponentUpdate`和`React.Memo`包在模态组件周围，以便在模态被解除后状态被修改时不会再次呈现页面，但没有成功。

这是可能的，同样，我也很乐意听到这方面的任何建议。

# 发布后更新

[Ely Lucas](https://twitter.com/elylucas) 谢天谢地[回复了](https://dev.to/elylucas/comment/imio)这篇博文，给出了一个有效且有趣的评论:实际上，不使用按钮也可以解除 modal。例如通过敲击 Esc 键或点击背景。

因此，在任何情况下都必须定义一个`onDidDismiss`的处理程序，以便在对话框关闭后同步打开状态(正如我们在上一章中所做的)。

# 概述

通过`callack`或`reference`按钮关闭模态都是可能的。

另一方面，监听`onDidDismiss`以将状态设置为`false`，即使它触发了重新渲染，也是一种强制。

# 传递参数

在前面的例子中，我们已经使用了一个属性来传递一个回调以关闭模式。同样，我们可以使用相同的方法来定义任何其他属性。

```
import React from 'react';
import {IonHeader, IonContent, IonToolbar, IonTitle, IonButtons, IonButton, IonIcon} from '@ionic/react';

type MyModalProps = {
  closeAction: Function;
  text: string;
}

class MyModal extends React.Component<MyModalProps> {

  render() {
    return <>
      <IonHeader>
        <IonToolbar color="primary">
          <IonTitle>My Modal</IonTitle>
          <IonButtons slot="end">
            <IonButton onClick={() => this.props.closeAction()}>
              <IonIcon name="close" slot="icon-only"></IonIcon>
            </IonButton>
          </IonButtons>
        </IonToolbar>
      </IonHeader>
      <IonContent className="ion-padding">
        <p>{this.props.text}</p>
      </IonContent>
    </>
  };

}

export default ({closeAction, text}: { closeAction: Function, text: string }) => (
  <MyModal closeAction={closeAction} text={text}>
  </MyModal>
)
```

因此，将页面中的任何其他参数传递给我们的模态组件。

```
import React, {useState} from 'react';
import {IonModal, IonButton, IonContent} from '@ionic/react';
import MyModal from './MyModal';

export const Tab1: React.FC = () => {
  const [showModal, setShowModal] = useState(false);

  async function closeModal() {
    await setShowModal(false);
  }

  return (
    <IonContent>
      <IonModal isOpen={showModal}>
        <MyModal closeAction={closeModal}
                 text="This is the updated modal content.">
        </MyModal>
      </IonModal>
      <IonButton onClick={() => setShowModal(true)}>
        Show Modal
      </IonButton>
    </IonContent>
  );
};

export default Tab1;
```

到无限和更远的地方。

大卫