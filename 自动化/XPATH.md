# XPath
测试地址：www.baidu.com
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814154519.png)
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814154551.png)

- 1.节点 标签 [@属性="属性值"]   多条件用and
`//input[@id="kw" and @name="wd"]`
- 2.节点 标签 [text()="文本值"]
`//span[text()="按图片搜索"]`
- 3.节点 标签 [contains(参数1，参数2)]  参数1：属性，text() ；参数2：模糊匹配的值
`//input[contains(@id,"k")]`
`//*[contains(text(),"按图片")]`
- 4.节点 标签 [starts-with(参数1，参数2)]    参数1：属性，text() ；参数2：以xx开头
`//input[starts-with(@id,"k")]`
`//*[starts-with(text(),"按图片")]`
- 5.上下级查询   /../标签[n]
`//*[starts-with(text(),"按图片")]/../span`
- 6.svg(特殊)
`//*[name()="svg"]`
- 7.XPath轴：  当前节点/XPath轴 :: 目标节点
`//span[contains(@class,"s_ipt_wr")]/ancestor::*[@id="head"]`
当前span标签 祖先中id是head的标签
![](https://gitee.com/sinoeast/imgs/raw/master/img/20220814154352.png)
