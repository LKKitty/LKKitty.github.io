---
title: 根据pid构建树状结构
date: 2020-04-02 09:11:14
tags:
   - js
   - 算法
---
现在有一个对象组成的数组，每个元素有id属性和parentId属性，根据其id属性和parentId属性，将其转换为树结构的对象
<!-- more -->
## 解答
数据
```js
let arr =[
    {id:2,name:'部门B',parentId:0},
    {id:3,name:'部门C',parentId:1},
    {id:1,name:'部门A',parentId:2},
    {id:4,name:'部门D',parentId:1},
    {id:5,name:'部门E',parentId:2},
    {id:6,name:'部门F',parentId:3},
    {id:7,name:'部门G',parentId:2},
    {id:8,name:'部门H',parentId:4}
];
```
非递归求解
```js
/**
 * 数组转树  非递归求解
 * 利用数组和对象相互引用  时间复杂度O(n)
 * @param {Object} list
 */
 function totree(list,parId) {
    let obj = {};
    let result = [];
    //将数组中数据转为键值对结构 (这里的数组和obj会相互引用)
    list.map(el => {
        obj[el.id] = el;
    })
    for(let i=0, len = list.length; i < len; i++) {
        let id = list[i].parentId;
        if(id == parId) {
            result.push(list[i]);
            continue;
        }
        if(obj[id].children) {
            obj[id].children.push(list[i]);
        } else {
            obj[id].children = [list[i]];
        }
    }
    return result;
}
let result = totree(arr,0)
console.log(result)
```
递归求解
```js
/**
 * 数组转树  递归求解
 * 利用数组和对象相互引用 
 * @param {Object} list
 */
function toTree(list,parId){
	let len = list.length
	function loop(parId){
		let res = [];
		for(let i = 0; i < len; i++){
			let item = list[i]
			if(item.parentId === parId){
				item.children = loop(item.id)
				res.push(item)
			}
		}
		return res
	}
	return loop(parId)
}
let result = totree(arr,0)
console.log(result)
```
深度优先遍历  堆栈  后进先出
```js
/**
 * 树转数组扁平化结构   
 * 深度优先遍历  堆栈  后进先出
 */
function deep(node){
	let stack = node,
		data = [];
	while(stack.length != 0){
		let pop = stack.pop();
		data.push({
			id: pop.id,
			name: pop.name,
			parentId: pop.parentId
		})
		let children = pop.children
		if(children){
			for(let i = children.length-1; i >=0; i--){
				stack.push(children[i])
			}
		}
	}
	return data
}
```
```js
/**
 * 树转数组扁平化结构   
 * 深度优先遍历  堆栈  后进先出
 */
function wideTraversal(node){
	let stack = node,
		data = [];
	while(stack.length != 0){
		let shift = stack.shift();
		data.push({
			id: shift.id,
			name: shift.name,
			parentId: shift.parentId
		})
		let children = shift.children
		if(children){
			for(let i = 0; i < children.length; i++){
				stack.push(children[i])
			}
		}
	}
	return data
}
```
# 参考（转载）资料
<a href="https://blog.csdn.net/susuzhe123/article/details/95353403">js数组和树结构数据相互转换</a>