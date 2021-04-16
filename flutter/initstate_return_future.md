## 错误： 在initState的初始化返回Future类型


### 1.错误代码
``` dart
class _IssueEnvelopesState extends State<IssueEnvelopes>{
  @override
   initState()  async {
    super.initState();
    logger.i("------------------- initState ----------------------");
    List<RedEnvelopModel> redEnvelopList =[];
    redEnvelopService=new RedEnvelopService();
    List<RedEnvelopModel>    redEnvelopList= await redEnvelopService.getIssueRedEnvelopList(this.page,this.limit) ;

    setState(() {
      this.redEnvelopList=redEnvelopList;
    });
    }
    
    
    ///略
}
```




### 2. 错误详情：初始化的时候请求从服务器异步请求数据,异步返回数据时  `async` 返回数据类型 `Future` 
```text
The following assertion was thrown building KeyedSubtree-[<1>]:
_IssueEnvelopesState.initState() returned a Future.

State.initState() must be a void method without an `async` keyword.

Rather than awaiting on asynchronous work directly inside of initState,
 call a separate method to do this work without awaiting it.
```

### 3. 错误原因  initState方法 初始化时 不允许返回任何类型 


### 4. 解决方案 新建一个方法在 initState 方法里调用,避免 `async` 返回future 类型 


```dart


  initState() {
    super.initState();
    lording();
  }

  lording() async {
    redEnvelopService = new RedEnvelopService();
    List<RedEnvelopModel> redEnvelopList =
        await redEnvelopService.getIssueRedEnvelopList(this.page, this.limit);
    setState(() {
      this.redEnvelopList = redEnvelopList;
    });
  }

```