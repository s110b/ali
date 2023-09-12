# 目前批量修改运单有如下规则：
- 校验订单是否存在。 【查询订单表】
- 订单导入的运单号数量 与 原发货信息不一致。导入失败。  单包 多包校验
- 校验承运商是否在承运商列表 【tms接口】  List<CarriersCodeShortname> getCarriers(String carriersCode) 
> 同订单号下承运商承运商值不一样，此订单都不会做任何操作

1. [新文章](xinwenzhang)
2. [工作](work)
3. [为vs code 增加markdown 自定义代码提示](markdown_snippet)