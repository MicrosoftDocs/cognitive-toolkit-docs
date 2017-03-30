You might be wondering if Dropout applied to a Constant is dynamically evaluated. It is! Therefore [Zoneout](http://arxiv.org/abs/1606.01305) is as simple as 
```
Zoneout (x, xprev) =
{
    mask = Dropout (BS.Constants.OnesLike (x))
    res = BS.Boolean.If (mask, xprev, x)
}.res
```
