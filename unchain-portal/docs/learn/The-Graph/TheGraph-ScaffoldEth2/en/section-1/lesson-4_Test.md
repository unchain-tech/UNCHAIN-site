## Test

### ✅ Test your newly deployed Subgraph

Next, lets see if our data is in The Graph. Here is an example query that shows us the first message.

```
{
  sendMessages(first: 1, orderBy: blockTimestamp, orderDirection: desc) {
    message
    _from
    _to
  }
}
```

You should get a nice response like this:

![](./../../img/section-1/1_4_1.png)

Data is such a beautiful thing huh?
