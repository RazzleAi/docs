# Workspaces & Apps

Quite simply, a workspace is a collection of Razzle apps, and a Razzle app is a collection of Actions. Our idea in creating workspaces is that there are probably a qroup of Razzle apps created by our user that probably work together. For instance, if you are building an expense manager app, you might have an app for managing employees, another for managing expenses, and another for managing companies. These apps are probably going to be used together, so we created the concept of a workspace to group them together.

It's important to note that Razzle apps are note a barrier to accessing the actions withing them. They simplay furnish a workspace with new capabilities. So you can write a prompt that calls actions across multiple apps.

For instance typing:

```
Get me the list of employees for company with the most most money expensed in the last 30 days

```

Should call a theoretical action called `getCompanyWithHighestExpense("last 30 days", "today")` from the `expense manager` app which returns a company, and then call `getEmployeesByCompany(company.id)` from the `employee manager` app which returns a list of employees.

Now this level of integration is not possible with the current version of Razzle, but it is something we are going to release soon.

You can joins us on our [Discord](https://discord.gg/TzRt9wQM5u) to get the latest updates on our progress and ask questions.
