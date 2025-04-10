---
title: 'Spec: Group'
---

The Group API call is how you associate an individual user with a group, such as a company, organization, account, project, or team.

The Group call enables you to identify what account or organization your users are part of. There are two IDs that are relevant in a Group call: the `userId`, which belongs and refers to the user, and the `groupId`, which belongs and refers to the specific group. A user can be in more than one group which would mean different `groupId`s, but the user will only have one `userId` that is associated to each of the different groups. Keep in mind that not all platforms support multiple groups for a single user. 

{% include components/reference-button.html href="https://university.segment.com/introduction-to-segment/324252?reg=1&referrer=docs" icon="media/academy.svg" title="Segment University: The Segment Methods" description="Check out our high-level overview of these APIs in Segment University. (Must be logged in to access.)" %}

In addition to the `groupId`, which is how you'd identify the specific group or company, the group method receives traits that are specific to the group, like industry or number of employees for example, that belong to that specific account. Like the traits of an identify call, you can update these when you call the same trait with a different value.

When using the Group call, it's helpful if you have accounts with multiple users.


> info "Segment doesn't have an ungroup call"
> If you're using a device-mode destination that has a method for ungrouping users, you can invoke it directly on the client side [using Segment's ready() method](/docs/connections/sources/catalog/libraries/website/javascript/#ready). 
>
> For cloud-mode destinations, you can [create a Destination Function](/docs/connections/functions/destination-functions/) to ungroup users. 


Here's the payload of a typical Group call, with most [common fields](/docs/connections/spec/common/) removed:

```json
{
  "type": "group",
  "groupId": "0e8c78ea9d97a7b8185e8632",
  "traits": {
    "name": "Initech",
    "industry": "Technology",
    "employees": 329,
    "plan": "enterprise",
    "total billed": 830
  }
}
```

And here's the corresponding JavaScript event that would generate the above payload:

```js
analytics.group("0e8c78ea9d97a7b8185e8632", {
  name: "Initech",
  industry: "Technology",
  employees: 329,
  plan: "enterprise",
  "total billed": 830
});
```
{% include content/syntax-note.md %}

Beyond the common fields, the Group call takes the following fields:

<table>
  {% include content/spec-table-header.md %}
  {% include content/spec-field-group-id.md %}
  {% include content/spec-field-group-traits.md %}
  {% include content/spec-field-user-id.md %}
  {% include content/spec-field-anonymous-id.md %}
</table>


## Example

Here's a complete example of a Group call:

```js
{
  "anonymousId": "507f191e810c19729de860ea",
  "channel": "browser",
  "context": {
    "ip": "8.8.8.8",
    "userAgent": "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/40.0.2214.115 Safari/537.36"
  },
  "integrations": {
    "All": true,
    "Mixpanel": false,
    "Salesforce": false
  },
  "messageId": "022bb90c-bbac-11e4-8dfc-aa07a5b093db",
  "receivedAt": "2015-02-23T22:28:55.387Z",
  "sentAt": "2015-02-23T22:28:55.111Z",
  "timestamp": "2015-02-23T22:28:55.111Z",
  "traits": {
    "name": "Initech",
    "industry": "Technology",
    "employees": 329,
    "plan": "enterprise",
    "total billed": 830
  },
  "type": "group",
  "userId": "97980cfea0067",
  "groupId": "0e8c78ea9d97a7b8185e8632",
  "version": "1.1"
}
```

### Create your own Group call

Use the following interactive code pen to see what your Group calls would look like with user-provided information:

{% include components/codepens/group-spec.html %}

## Identities

{% include content/spec-identities.md %}

## Group ID

A Group ID is the unique identifier which you recognize a group by in your own database. For example, if you're using MongoDB it might look something like `507f191e810c19729de860ea`.


## Traits

Traits are pieces of information you know about a group that are passed along with the Group call, like `employees` or `website`.

Segment has reserved some traits that have semantic meanings for groups, and handles them in special ways. You should **only use reserved traits for their intended meaning**.

The following are the reserved traits Segment has standardized:

| **Trait**     | **Type** | **Description**                                                                                                                       |
|---------------|----------|---------------------------------------------------------------------------------------------------------------------------------------|
| `address`     | Object   | Street address of a group. This should be a dictionary containing optional `city`, `country`, `postalCode`, `state`, or `street`.                               |
| `avatar`      | String   | URL to an avatar image for the group.                  |
| `createdAt`   | Date     | Date the group's account was first created. Segment recommends [ISO-8601](http://en.wikipedia.org/wiki/ISO_8601){:target="_blank"} date strings.                   |
| `description` | String   | Description of the group, like their personal bio.     |
| `email`       | String   | Email address of group.             |
| `employees`   | String   | Number of employees of a group, typically used for companies.       |
| `id`          | String   | Unique ID in your database for a group.                |
| `industry`    | String   | Industry a user works in, or a group is part of.       |
| `name`        | String   | Name of a group.                    |
| `phone`       | String   | Phone number of a group.            |
| `website`     | String   | Website of a group.                 |
| `plan`        | String   | Plan that a group is in.            |

**Note:** You might be used to some destinations recognizing special properties differently. For example, Mixpanel has a special `track_charges` method for accepting revenue. Luckily, you don't have to worry about those inconsistencies. Just pass along `revenue`.  **Segment handles all of the destination-specific conversions for you automatically.** Same goes for the rest of the reserved properties.

If you pass these values, `on null` will throw a `NullPointerException`.
You may continue to set values inside the trait.  If you do so, this would work the same as the rules do with NoSQL data. If you had set a value previously for a user and on the next request you sent the same value of that property as `on null`, it will be replaced by `null`, but if you do not send that property, the original value is persisted.

**Traits are case-insensitive**, so in JavaScript you can match the rest of your camel-case code by sending `createdAt`, and in Ruby you can match your snake-case code by sending `created_at`. That way the API never seems alien to your code base.

