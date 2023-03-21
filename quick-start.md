# Quick start

### Sign up for a free account

Head over to [Razzle](https://app.getrazzle.com) and sign up for a free account. You can sign up with your email or with your github account.

<br />

### Create a new app

Once you have signed up, you will be redirected to the dashboard. From there you can create a new app in the apps section.

![Create app gif](assets/create-app.gif)

Copy the App ID and the API key. You will need them later.

<br />

## Flesh out your app

Now that you have created your app, you can start fleshing it out. You can do this by adding actions, descriptions and parameters in your code. You can do this either in a new project or in an existing project.

Start by installing the razzle sdk and the widgets library

```bash
npm install @razzledotai/sdk
npm install @razzledotai/widgets
```

**Important**: Razzle makes use of typescript decorators which depending on your typescript version will require you to make some modifications to your `tsconfig.json` file.

Please add the below code snippet to your `compilerOptions` section

```json
{
  "compilerOptions": {
    ...
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
  }
}
```

In your project you can initialize the sdk like in this example below

```typescript
Razzle.app({
  appId: process.env.RAZZLE_APP_ID, // You can also replace this environment variable with actual values
  apiKey: process.env.RAZZLE_API_KEY,
  modules: [{ }],
});
```

## Let's build

For the purpose of this documentation we will build a simple app. What's simpler than a todo list app?
We would have functionality for adding, removing and listing todos also for marking todos as done. Seems simple enough right?

<br />

## Now let's talk modules

<br />

Modules are the building blocks of your app. A module would typically be a class that contains a group of actions that can be performed by your Razzle App.

That being said, let's create a module for our todo app. This file the skeleton of our module.

Let's call the file `todo.module.ts`

```typescript
import { Action, ActionParam } from "@razzledotai/sdk";
import { RazzleResponse, RazzleText } from "@razzledotai/widgets";

export default class TodoModule {
  @Action({
    name: "createTodoList",
    description: "Creates a new todo list",
  })
  createTodoList(@ActionParam("name") name: string) {
    return new RazzleResponse({
      ui: new RazzleText({
        text: "Created todo list",
      }),
    });
  }

  @Action({
    name: "addTodo",
    description: "Adds a new todo to a todo list",
  })
  addTodo(
    @ActionParam("todoListName") todoListName: string,
    @ActionParam("todo") todo: string
  ) {
    return new RazzleResponse({
      ui: new RazzleText({
        text: "Added todo to list",
      }),
    });
  }

  @Action({
    name: "completeTodo",
    description: "Marks a todo as complete",
  })
  completeTodo(
    @ActionParam("todoListName") todoListName: string,
    @ActionParam("todo") todo: string
  ) {
    return new RazzleResponse({
      ui: new RazzleText({
        text: "Completed todo",
      }),
    });
  }

  @Action({
    name: "deleteTodo",
    description: "Deletes a todo from a todo list",
  })
  deleteTodo(
    @ActionParam("todoListName") todoListName: string,
  ) {
    return new RazzleResponse({
      ui: new RazzleText({
        text: "Deleted todo",
      }),
    });
  }
}

```
**...before we go any further, let's talk about `Actions`**

Actions are bassically a javascript methond inside a module and forms the smallest unit of functionality in your app. An action can be invoked by the user if they type in a prompt that our system matches to the action. Actions are denoted by the `@Action` decorator.

Action always return a `RazzleResponse` object. This object is used to send a response back to the user. The `RazzleResponse` can also be used to add addtional context to your users session via the `addToContext` property.

For now we are only going to use the `ui` property of the `RazzleResponse` object. This property is used to define the UI that will be displayed to the user. The UI can be a simple text message or a more complex UI that can be built using the widgets library.

<br />

**let's talk about the `@Action` decorator**

The `@Action` decorator is used to define an action. It takes in an object with the following properties

- `name` - The name of the action. This is the name that will be used to invoke the action. This is also the name that will be displayed in the Razzle App dashboard.
- `description` - The description of the action. This is the description that will be displayed in the Razzle App dashboard.

**now let's talk about the `@ActionParam` decorator**

The `@ActionParam` decorator is used to define an action parameter. It takes in an object with the following properties

- `name` - The name of the parameter. This is the name that will be used to invoke the action. This is also the name that will be displayed in the Razzle App dashboard.

Now that we have gotten that out of the way, let's add the module to our app.

```typescript

Razzle.app({
  appId: process.env.RAZZLE_APP_ID, 
  apiKey: process.env.RAZZLE_API_KEY,
  modules: [{ module: TodoModule, deps: [] }], // Add the module here
});

```


Let's start our app again and see what happens. You should see an `App Sync succcessful` message and looking at your razzle app your should see the new actions that you have created.

As a matter of fact you can already start playing around with your app. You can see samples in the examples below

<br />

![Create app gif](assets/test-skeleton.gif)

<br />

## Let's make our app more useful

Now we're going to introduce a new file called `models.ts`. This file will contain the shape of the todo list and the todo items objects.

```typescript

export interface TodoList {
  name: string;
  items: TodoListItem[];
}

export interface TodoListItem {
  name: string;
  completed: boolean;
}

```

Now I'm going to add a todo list object to the top of our `TodoModule` class. This object will be used to store our todo lists.

```typescript

export default class TodoModule {

  todoLists: TodoList[] = []; // new

  ...

}

```

Now let's update our `createTodoList` action to add the new todo list to our `todoLists` array.

```typescript

  @Action({
    name: "createTodoList",
    description: "Creates a new todo list",
  })
  createTodoList(@ActionParam("name") name: string) {
    this.todoLists.push({
      name: todoListName.toLowerCase(),
      items: [],
    });

    return new RazzleResponse({
      ui: new RazzleText({
        text: "Created todo list",
      }),
    });
  }

```

 then we update our `addTodo` action to add the new todo to the todo list.

 ```typescript

  @Action({
    name: "addTodo",
    description: "Adds a new todo to a todo list",
  })
  addTodo(
    @ActionParam("todoListName") todoListName: string,
    @ActionParam("todo") todo: string
  ) {
    const todoList = this.todoLists.find((list) => list.name === todoListName);

    if (!todoList) {
      return new RazzleResponse({
        ui: new RazzleText({
          text: "Todo list not found",
        }),
      });
    }

    todoList.items.push({
      name: todo,
      completed: false,
    });

    return new RazzleResponse({
      ui: new RazzleText({
        text: "Added todo to list",
      }),
    });
  }
 ```

Now let's update our `completeTodo` action to mark the todo as complete.

```typescript

  @Action({
    name: "completeTodo",
    description: "Marks a todo as complete",
  })
  completeTodo(
    @ActionParam("todoListName") todoListName: string,
    @ActionParam("todo") todo: string
  ) {
    const todoList = this.todoLists.find((list) => list.name === todoListName);

    if (!todoList) {
      return new RazzleResponse({
        ui: new RazzleText({
          text: `Todo list ${todoListName} not found`,
        }),
      });
    }

    const todoItem = todoList.items.find((item) => item.name === todo);

    if (!todoItem) {
      return new RazzleResponse({
        ui: new RazzleText({
          text: `Todo item ${todo} not found in todo list ${todoListName}`,
        }),
      });
    }

    // Update the todo list

    this.todoLists = this.todoLists.map((list) => {
      if (list.name === todoListName) {
        return {
          ...list,
          items: list.items.map((item) => {
            if (item.name === todo) {
              return {
                ...item,
                completed: true,
              };
            }

            return item;
          }),
        };
      }

      return list;
    });

    return new RazzleResponse({
      ui: new RazzleText({
        text: "Completed todo",
      }),
    });
  }

```

Now let's add some code to delete a todo list.

```typescript

  @Action({
    name: "deleteTodoList",
    description: "Deletes a todo list",
  })
  deleteTodoList(@ActionParam("name") name: string) {
    this.todoLists = this.todoLists.filter((list) => list.name !== name);

    return new RazzleResponse({
      ui: new RazzleText({
        text: "Deleted todo list",
      }),
    });
  }

```

Now that we've updated our module let's take our app for a spin! Restart the app and go to the Razzle App dashboard.

Let's see a preview

![Create app gif](assets/triggering-actions.gif)

<br />

## Don't be afraid to get exotic.

You don't have to type in exact phrases to trigger your actions. You can use synonyms and even abbreviations. For example, you can say `create a todo list` or `create todo list` or `create a todo` or `create todo` or `add a new todo list` and razzle should be able to understand what you mean and route your requests to the appropriate action. You can also write in prompts that take multiple actions at once.