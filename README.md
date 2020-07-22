<h1 align="center">
  <a href="https://redux-model.github.io/redux-model">
    Redux Model
  </a>
</h1>

[English](./README-EN.md)

Redux Model的存在是为了弥补原生Redux繁琐的开发流程，开发者重复劳动效率低下，模板文件导致代码量臃肿，以及因action和reducer文件分散造成代码追踪困难的问题而设计的。

[![License](https://img.shields.io/github/license/redux-model/redux-model)](https://github.com/redux-model/redux-model/blob/master/LICENSE)
[![GitHub Workflow Status (branch)](https://img.shields.io/github/workflow/status/redux-model/redux-model/CI/master)](https://github.com/redux-model/redux-model/actions)
[![Codecov](https://img.shields.io/codecov/c/github/redux-model/redux-model)](https://codecov.io/gh/redux-model/redux-model)


# 特性

* 代码量极简，超高开发效率
* 使用mvvm更改reducer，一步到位
* **基于typescript定制，拥有100%无死角的业务代码类型提示**
* 每个请求的action都自带loading状态记录
* 支持 Hooks


# 安装

### React 或 React-Native
```bash
npm install @redux-model/react redux react-redux
```

### Vue v3
```bash
npm install @redux-model/vue redux
```

### Taro v3
```bash
npm install @redux-model/taro redux react-redux
```

### 其它
* 对于Taro 3.0之前的版本，请安装 **@redux-model/taro@6.9.2**
* 对于Vue 3.0之前的版本，请安装 **@redux-model/vue@6.9.2**

# 定义模型
```typescript
interface Response {
    id: number;
    name: string;
}

interface Data {
    counter: number;
    users: Partial<{
        [key: string]: Response;
    }>;
}

class TestModel extends Model<Data> {
    increase = this.action((state) => {
        state.counter += 1;
    });

    getUser = $api.action((id: number) => {
        return this
            .get<Response>(`/api/user/${id}`)
            .onSuccess((state, action) => {
                state.counter += 1;
                state.users[id] = action.response;
            });
    });

    protected initReducer(): Data {
        return {
            counter: 0,
            users: {},
        };
    }
}

export const testModel = new TestModel();
```

# 在React Hooks中使用
```typescript jsx
import React, { FC } from 'react';

const App: FC = () => {
    const counter = testModel.useData((data) => data.counter);
    const loading = testModel.getUser.useLoading();

    const increase = () => {
        testModel.increase();
        testModel.getUser(3);
    };

    return (
        <button onClick={increase}>
            {loading ? 'Waiting...' : `You clicked ${counter} times`}
        </button>
    );
};

export default App;
```

# 在Vue Hooks中使用
```vue
<template>
  <button @click="increase">
    {{loading.value ? 'Waiting...' : `You clicked ${counter.value} times`}}
  </button>
</template>

<script>
export default {
    setup() {
        const increase = () => {
            testModel.increase();
            testModel.getUser(3);
        };

        const counter = testModel.useData((data) => data.counter);
        const loading = testModel.getUser.useLoading();

        return {
            increase,
            counter,
            loading,
        };
    }
};
</script>
```

# 在Redux Connect中使用
```typescript jsx
import React, { Component } from 'react';
import { connect } from 'react-redux';

type Props = ReturnType<typeof mapStateToProps>;

class App extends Component<Props> {
    increase() {
        testModel.increase();
        testModel.getUser(3);
    }

    render() {
        const { loading, counter } = this.props;
        return (
            <button onClick={this.increase}>
                {loading ? 'Waiting...' : `You clicked ${counter} times`}
            </button>
        );
    }
}

const mapStateToProps = () => {
    return {
        counter: testModel.data.counter,
        loading: testModel.getUser.loading,
    };
};

export default connect(mapStateToProps)(App);
```

# 简单的例子

* [React Web](https://github.com/redux-model/redux-model-web-demo)

# 文档

请点击[这里查看文档](https://redux-model.github.io/redux-model)

---------------------

欢迎使用并随时给我建议
