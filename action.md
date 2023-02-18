## @Action

The `@Action` decorator is used to mark a method as an action that can be performed by our razzle app. The decorator takes in an object with the following properties

- `name` - The name of the action
- `description` - A detailed description of the action
- `stealth (optional)` - A boolean that determines if the action should be hidden from the user. This is useful for actions that are meant to be used internally by other actions

For instance this [code block](https://github.com/RazzleAi/demo/blob/b1a12915878a92be3227fedbaaf29ff67fa811ce/src/expense-manager.ts#L62)

```ts

@Action({
    name: "getCompany",
    description: "Get a company",
    stealth: true,
  })
  getCompany(@ActionParam("companyId") id: string) {
    const company = this.expenseManagerService.getCompany(id);
    return new RazzleResponse({
      ui: new RazzleList({
        title: company.name,
        items: [
          {
            text: "Manage",
            actions: [
              {
                action: "getEmployeesByCompany",
                label: "List employees",
                args: [company.id],
              },
            ],
          },
        ],
      }),
    });
  }

```
