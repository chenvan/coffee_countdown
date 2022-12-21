# 2022.12.15
- [] ~~增加 nanoid~~(似乎不需要, 由于数据是 array, 删除时只需要 index 就行)

## edit 页面
- [x] uni-checkbox 换成 内置checkbox
- [] ~~uni-form 换成 内置 form~~

# 2022.12.14
## plan 页面
- [x] 使用短按进入编辑页面
- [] 长按进入删除模式(参考小米时钟), 
- [x] 新增按钮需要悬浮
- [] plan 组件


#
onLoad
state 是 loading
获得 this.plans(可能是空 list) 和 this.lastSelect

computed
chosenMode 返回 this.plans[this.lastSelect] (可能是 undefined)

watch
chosenMode (换 模式 的时候, 需要清零计时)
