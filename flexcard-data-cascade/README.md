# Data cascading from parent to child Flexcards

There are two ways to provide child Flexcards with data:

- Using data nodes
- Using attributes

The data nodes option is quite useful especially if you have a data model where you want to group records under a complex hierarchy that cannot be realised through conventional means such as lookup fields or master-detail relationships.

An example would be different brokerages with different branches where different policies are registered at:

```
└── Brokerage A
    └── Branch AA
        └── Policy AAC
    └── Branch AB
        ├── Policy ABA
        └── Policy ABB
    └── Branch AC
        ├── Policy ACA
        ├── Policy ACB
        └── Policy ACC
└── Brokerage B
    └── Branch BA
        ├── Policy BAA
        └── Policy BAB
└── Brokerage C
    └── Branch CA
        └── Policy CAB
    └── Branch CB
        ├── Policy CBA
        └── Policy CBB
```

With one Data Raptor you can extract the JSON payload that will have all the necessary information, but how do you dynamically display it and ensure that the Flexcard can grow with any additions of any branches/policies in the future?

The best way to achieve this is to pass a list to the Flexcard and let it repeat itself the number of times the size of the list.

If we want to create a Flexcard that displays the policies for all relevant branches for a given brokerage, the JSON payload will look something like this:

```json
{
  "Brokerage": {
    "BrokerageName": "Brokerage A",
    "Branch": [
      {
        "BranchName": "Branch AA",
        "Policies": [
          {
            "PolicyId": "0YT3L000000OYVaWAO",
            "PolicyName": "Policy AAA",
            "InsuredName": "PolicyHolder AAA"
          }
        ]
      },
      {
        "BranchName": "Branch AB",
        "Policies": [
          {
            "PolicyId": "0YT3L000000OYVZWA4",
            "PolicyName": "Policy ABA",
            "InsuredName": "PolicyHolder ABA"
          },
          {
            "PolicyId": "0YT3L000000OYVeWAO",
            "PolicyName": "Policy ABB",
            "InsuredName": "PolicyHolder ABB"
          }
        ]
      },
      {
        "BranchName": "Branch AC",
        "Policies": [
          {
            "PolicyId": "0YT3L000000OYVZWA4",
            "PolicyName": "Policy ACA",
            "InsuredName": "PolicyHolder ACA"
          },
          {
            "PolicyId": "0YT3L000000OYVeWAO",
            "PolicyName": "Policy ACB",
            "InsuredName": "PolicyHolder ACB"
          },
          {
            "PolicyId": "0YT3L000000OYVeWAO",
            "PolicyName": "Policy ACC",
            "InsuredName": "PolicyHolder ACC"
          }
        ]
      }
    ]
  }
}
```

Three Flexcards will be required to efficiently display the data:

1. Parent Flexcard (embedded on the Brokerage Lightning Record page)
2. Child Flexcard (Branch)
3. Child Flexcard (Policy)

Starting with the inner most child Flexcard (which is displaying the policies), you create a child Flexcard and add a Custom data source with the example JSON payload:

```json
[
  {
    "PolicyId": "0YT3L000000OYVZWA4",
    "PolicyName": "Policy ACA",
    "InsuredName": "PolicyHolder ACA"
  },
  {
    "PolicyId": "0YT3L000000OYVeWAO",
    "PolicyName": "Policy ACB",
    "InsuredName": "PolicyHolder ACB"
  },
  {
    "PolicyId": "0YT3L000000OYVeWAO",
    "PolicyName": "Policy ACC",
    "InsuredName": "PolicyHolder ACC"
  }
]
```

After the data source is saved, you can drag multiple Field elements onto the canvas, add some styling if you want and preview it. The Flexcard will repeat for each entry in the JSON list:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/flexcard-data-cascade/images/policiesFlexcard.png)

The Flexcard one step higher in the hierarchy is displaying all the branches for the given brokerage. This Flexcard will use the first child Flexcard we created. Again a Custom data source is used and the JSON payload for this Flexcard will be:

```json
[
  {
    "BranchName": "Branch AA",
    "Policies": [
      {
        "PolicyId": "0YT3L000000OYVaWAO",
        "PolicyName": "Policy AAA",
        "InsuredName": "PolicyHolder AAA"
      }
    ]
  },
  {
    "BranchName": "Branch AB",
    "Policies": [
      {
        "PolicyId": "0YT3L000000OYVZWA4",
        "PolicyName": "Policy ABA",
        "InsuredName": "PolicyHolder ABA"
      },
      {
        "PolicyId": "0YT3L000000OYVeWAO",
        "PolicyName": "Policy ABB",
        "InsuredName": "PolicyHolder ABB"
      }
    ]
  }
]
```

Add a Block element to the Flexcard and make it collapsible. Set the Block Label as the `BranchName`. Now each Branch will have a collapsible block that houses a list of all the policies registered with that branch.

Now we add the child Flexcard element to this Flexcard and enter the name of the first child Flexcard we created.

The Data Node field will give you a bunch of options when you click on it:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/flexcard-data-cascade/images/dataNodeOptions.png)

From the above JSON payload, we want to give the child Flexcard all the `Policies` for all the Branch. The options listed does not give us that option. The option `{records[0].Policies}` will only give the first Branch’s Policies and similarly the `{records[1].Policies}` will only pass along the second Branch’s Policies where we want to give the child Flexcard **all** the Branches’ Policies.

The solution is to type in `{record.Policies}` which sends the correct payload to the child Flexcard:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/flexcard-data-cascade/images/branchFlexcard.png)

The final Flexcard is the parent Flexcard which will be embedded on the Lightning Record page. We can create another Block Element that is collapsible to save some space, especially if it is quite a busy brokerage with a lot of branches and policies.

The Data Node for the parent Flexcard is `{record.Branch}` and luckily it is an option to choose from:

![](https://github.com/sfadriaan/Omnistudio-Projects/blob/main/flexcard-data-cascade/images/brokerageFlexcard.gif)