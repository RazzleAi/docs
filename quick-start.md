## Razzle quick start

### Sign up for a free account

Head over to [Razzle](https://app.getrazzle.com) and sign up for a free account. Right now we are in beta and you can only signup with a gmail account. Follow the instructions to create your account until you arrive at chat screen.

### Create a new app

You can create a new razzle app by simple telling razzle to :). Just type something like this in the text box and press enter.

`Create a new app called Razzle that is meant for my personal use`

You can vary this ☝️ prompt to create an app but the format should be something like this.

`Create a new app called <app name> that is meant for <app purpose>`

... something like this

`Create an app for managing an e-commerce store called commerce bot`

You expect to see something like this

[Create app gif]

Copy the App ID and the API key. You will need them later.

### Flesh out your app

Now that you have created your app, you can start fleshing it out. You can do this by adding actions, descriptions and parameters in your code.

Start by install the razzle sdk and the widgets library

```bash
npm install @razzledotai/sdk
npm install @razzledotai/widgets

```

Please note that we only support nodejs for now. We will be adding support for other languages soon.

In you project you can initialize the sdk like this

```js
Razzle.app({
  appId: process.env.RAZZLE_APP_ID,
  apiKey: process.env.RAZZLE_API_KEY,
  modules: [{}],
});
```

## Now let's talk modules

Modules are the building blocks of your app. A module would typically be a class that contains a group of actions that can be performed by your Razzle App

## Building the Razzle App

Let's create a module for our app. We will call it `expense-manager.ts`. Let's create it as an empty class for now.

```ts
export class ExpenseManagerModule {}
```

Now let's register on the on the Razzle app.

```ts
Razzle.app({
  appId: process.env.RAZZLE_APP_ID,
  apiKey: process.env.RAZZLE_API_KEY,
  modules: [
    // Register the module here with the service as a dependency
    { module: ExpenseManagerModule, deps: [new ExpenseManagerService()] },
  ],
});
```

Here we registering the module and also registering the service as a dependency so it will be injected into the module constructor.

Let's flesh out our module. We will add a new action to show us all the companies available in our app.

```ts
import { Action, ActionParam } from "@razzledotai/sdk";

export class ExpenseManagerModule {
  constructor(private readonly expenseManagerService: ExpenseManagerService) {}

  @Action({
    name: "getCompanies",
    description: "Get all companies",
  })
  getCompanies(): RazzleResponse {
    const companies = this.expenseManagerService.listCompanies();
  }
}
```

At the entry point of our app (usually `index.ts` or `main.ts`), let's start a server so we can have a long running process.

It should look something like this.

```ts
function startApp() {
  Razzle.app({
    appId: process.env.RAZZLE_APP_ID,
    apiKey: process.env.RAZZLE_API_KEY,
    modules: [
      // { module: TodoModule, deps: [new TodoService()] },
      { module: ExpenseManagerModule, deps: [new ExpenseManagerService()] },
    ],
  });

  const server = http.createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.write("Hello World!");
    res.end();
  });

  server.listen(7000, "localhost", () => {
    console.log("Server started on port 7000");
  });
}

startApp();
```

we should also include a tsconfig file that enables experimental decorators and an ES5 target. Here is an example.

```json
{
  "compileOnSave": false,
  "compilerOptions": {
    "rootDir": "src",
    "outDir": "dist",
    "sourceMap": true,
    "experimentalDecorators": true,
    "emitDecoratorMetadata": true,
    "target": "ES5"
  }
}
```

Build the app by running

```bash
tsc
```

or

if you don't have typescript installed globally.

```bash
npx tsc
```

Now let's run the app.

```bash
node dist/main.js
```

You should see an `App Sync succcessful` message in the console. This means the app has been successfully synced with Razzle.

Now let's test our app. Open razzle and refresh the page. In other to use the app you just created you need to add it to a workspace.

You can do this by simply telling razzle that's what you want to do.

You can do this by typing

`"I want to add some apps to this workspace"`

[Add app gif]

You should be presented with a list of apps. Select "Add to workspace" on the app you just created.

You should see a confirmation message that the app has been added to the workspace.

Let's update our action to return a response that razzle can understand. As a rule of thumb you should always return a `RazzleResponse` object from your actions. `RazzleResponse` is found in the `@razzledotai/widgets` package.

so let's update the action to look like this.

```ts

getCompanies(): RazzleResponse {
    const companies = this.expenseManagerService.listCompanies();

    // Return a RazzleResponse object

    return new RazzleResponse({
      ui: new RazzleContainer({
        padding: WidgetPadding.all(10),
        title: "All Companies",
        body: new RazzleColumn({
          children: companies.map((company) => {
            return new RazzleColumn({
              crossAxisAlignment: "center",
              children: [
                new RazzleText({ text: company.name }),
                new RazzleRow({
                  spacing: 20,
                  mainAxisAlignment: "center",
                  children: [
                    new RazzleLink({
                      action: {
                        action: "getCompany",
                        label: "Manage",
                        args: [company.id],
                      },
                    }),
                    new RazzleLink({
                      action: {
                        action: "listCardsByCompany",
                        label: "List cards",
                        args: [company.id],
                      },
                    }),
                  ],
                }),
              ],
            });
          }),
        }),
      }),
    });
  }

```

If you notice in the code about the `RazzleResponse` has a UI property. This is how we declare UI elements to display our data or collect data from the user.

Razzle utilizes a declarative UI framework similer to the one used in Flutter, SwiftUI or Jetpack Compose. You can see more on how to build UIs with Razzle [here](widgets/index.md).

Let's test our app again,

You can type

`Show me all the companies available `

This should return a list of companies. See image below.

[Get companies image]

Congratulations! You have successfully built your first Razzle app. Now let's add some more actions to our app.

The next action will list out all the employees a company has. Simply by supplying the company id.

```ts

@Action({
    name: "getEmployeesByCompany",
    description: "Get all employees by company",
  })
  getEmployeesByCompany(@ActionParam("companyId") id: string) {
    const employees = this.expenseManagerService.listEmployeesByCompany(id);
    const data: string[][] = [];
    for (const employee of employees) {
      data.push([employee.firstName, employee.lastName, employee.email]);
    }
    return new RazzleResponse({
      ui: new RazzleContainer({
        body: new RazzleColumn({
          children: [
            new RazzleText({ text: "All Employees" }),
            new RazzleTable({
              columns: [
                {
                  id: "firstName",
                  header: "First Name",
                },
                {
                  id: "lastname",
                  header: "Last Name",
                },
                {
                  id: "email",
                  header: "Email",
                },
              ],
              data: data,
            }),
          ],
        }),
      }),
    });
  }
```

Let's test our app again. You will need to restart the app to see the changes. Once that is done you can type

`Show me all the employees for company C1203939`

You should see a table with all the employees for the company you requested for. Please note you don't have to type the same prompts as the ones here. You can can type whatever you want and Razzle will try to figure out what you mean and match you to the right action.

[Get employees image]

You can type for example:

`Who are the employees for company with ID C1203939`

You can find a full implementation of the app [here](https://github.com/RazzleAi/demo)
