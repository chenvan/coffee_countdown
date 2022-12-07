# 序列

# Todo
- [] 存储倒计时方案
- [] 编辑页面
- [] 关于页面 

# 数据结构
## lastSelect 
number

## plans 
[mode, ...]

### [mode]
{
	name: string,
	initCd: number,
	keyPtCd: number,
	keyPts: [number, ...]
}

# 如何更新数据
通过 globalData 使 plans, lastSelect 可以通过页面交流

lastSelect 删除的时候, lastSelect也需要更新

