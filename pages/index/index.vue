<template>
	<view class="content">
		<!-- 有数据的情况 -->
		<view class="todo-header" v-if="list.length!==0">
			<view class="todo-header_left">
				<text class="active-text">{{text}}</text>
				<text>{{listData.length}}条</text>
			</view>
			<view class="todo-header_right">
				<view class="todo-header_right-item " @click="tab(0)" :class="{'active-tab':activeIndex===0}">全部</view>
				<view class="todo-header_right-item" @click="tab(1)" :class="{'active-tab':activeIndex===1}">待办</view>
				<view class="todo-header_right-item" @click="tab(2)" :class="{'active-tab':activeIndex===2}">已完成</view>
			</view>
		</view>
		<!-- 没有数据的情况 -->
		<view v-if="list.length===0">
			<view class="defaault_onfo">
				<view class="defaault_onfo_textup">您还没有创建任何数据</view>
				<view class="defaault_onfo_textdown">点击下面加号进行创建</view>
			</view>	
		</view>
		<!-- 任务列表 -->
		<view v-else class="todo-content">
			<view class="todo-list " v-for="(item,index) in listData" :key="index" :class="{'todo-finish':item.checked}" @click="finish(item.id)">
				<view class="todo-list_checkbox ">
					<view class="checkbox" ></view>
				</view>
				<view class="todo-list_content">
					{{item.content}}
				</view>
			</view>
		</view>
		<!-- 创建Todo -->
		<view class="create-todo" @click="create">
			<text class="iconfont icon-jia" :class="{'create-todo-active':textShow}"></text>
		</view>
		<!-- 添加 -->
		<view class="create-content" v-if="active" :class="{'create-show':textShow}">
			<!-- 输入框 -->
			<view class="create-input">
				<input type="text" v-model="value" placeholder="请输入您要创建的todo" />
			</view>
			<!-- 点击创建按钮 -->
			<view class="create-button" @click="add">
				创建
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				list:[
					// {
					// 	content:'test1'
					// },
					// {
					// 	content:'test2'
					// }
				],
				active:false,
				value:'',
				activeIndex:0,
				text:'全部',
				textShow:false
				
			}
		},
		onLoad() {

		},
		computed:{
			
			listData(){
				let list=JSON.parse(JSON.stringify(this.list))
				let newList=[]
				if(this.activeIndex===0){
					this.text='全部'
					return list
				}
				if(this.activeIndex===1){
					this.text='待办'
					list.forEach(item=>{
						if(!item.checked){
							newList.push(item)
						}
					})
					return newList
				}
				if(this.activeIndex===2){
					this.text='已完成'
					list.forEach(item=>{
						if(item.checked){
							newList.push(item)
						}
					})
					return newList
				}
				
			}
		},
		methods: {
			//打开输入框
			create(){
				if(this.active){
					this.close()
				}else{
					this.open()
				}
			},
			//打开动画
			open(){
				this.active=true
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow=true
					},50)
				})
			},
			//关闭动画
			close(){
				this.active=false
				this.$nextTick(()=>{
					setTimeout(()=>{
						this.textShow=false
					},50)
				})
			},
			//添加任务
			add(){
				if(this.value===''){
					uni.showToast({
						title:"请输入内容",
						icon:none,
					})
					return
				}
				this.list.unshift({
					id:'id'+new Date().getTime(),
					content:this.value,
					checked:false
				})
				this.value=''
				this.active=false
				this.close()
			},
			finish(id){
				let index=this.list.findIndex((item=>item.id===id))
				this.list[index].checked=!this.list[index].checked
			
			},
			tab(index){
				this.activeIndex=index
			}
		}
	}
</script>

<style>
@import '../../common/icon.css';
.todo-header{
	position: fixed;
	top: 0;
	left:0;
	width: 100%;
	diSplay:flex;
	align-items: center;
	padding:0 15px;
	font-size: 12px;
	color:#333333;
	height:45px;
	box-shadow: -1px 1px 5px 0 rgb(0,0,0,0.1);
	background:#FFFFFF;
	z-index: 999;
	box-sizing: border-box;
}
.todo-header_left{
	width: 100%;
}
.todo-header_right{
	display: flex;
	flex-shrink:0;
}
.todo-header_right-item{
	padding: 0  5px;
}
.active-tab{
	color:#279abf;
}
.active-text{
	font-size: 14px;
	color:#279abf;
	padding-right: 10px;
}

.todo-list{
	position: relative;
	display: flex;
	align-items: center;
	padding: 15px;
	margin: 15px;
	border-radius:10px;
	background-color: #cfebfb;
	color: #0c3854;
	box-shadow: -1px 1px 5px 1px rgba(0,0,0,0.1);
	overflow: hidden;
}
.todo-content{
	position: relative;
	padding-top: 45px;
	padding-bottom: 100px;
}
.todo-list:after{
	position: absolute;
	content: '';
	top:0;
	left: 0;
	bottom: 0;
	width: 4px;
	background-color: #a6dcef;
}
.checkbox{
	width: 20px;
	height: 20px;
	border-radius: 50%;
	background-color: #FFFFFF;
	box-shadow: 0 0 5px 1px rgba(0,0,0,0.1);
}
.todo-list_checkbox{
	padding-right: 15px;
}
.todo-finish .checkbox{
	position: relative;
	background: #eee;
}
.todo-finish .checkbox::after{
	content: '';
	position: absolute;
	width: 10px;
	height: 10px;
	top: 0;
	left: 0;
	right: 0;
	bottom: 0;
	margin: auto;
	background-color: #CFEBFD;
	border-radius: 50%;
	box-shadow: 0 0 2px 0 rgba(0,0,0,0.1) inset;
}
.todo-finish .todo-list_content{
	color: #999999;
}
.todo-finish.todo-list:before{
	content: '';
	position:absolute ;
	top:0;
	bottom: 0;
	left: 40px;
	right: 10px;
	height: 2px;
	margin: auto 0;
	background: #bdcdd8;
}
.todo-finish.todo-list::after{
	background-color: #CCCCCC;
}
.create-todo{
	display: flex;
	justify-content: center;
	align-items: center;
	position: fixed;
	bottom: 20px;
	left: 0;
	right: 0;
	margin: 0 auto;
	width: 50px;
	height: 50px;
	border-radius: 50%;
	background-color: #cfebfb;
	box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
}
.icon-jia{
	font-size: 30px;
	color:#FFFFFF;
}
.create-content{
	display: flex;
	align-items: center;
	position: fixed;
	bottom: 95px;
	left: 20px;
	right: 20px;
	padding: 15px;
	border-radius: 15px;
	background-color: #F8F8F8;
	box-shadow: -1px 1px 5px 2px rgba(0,0,0,0.1);
	transition: all 0.3s;
	opcaity:0;
	transform: scale(0) translateY(200%);
}
.create-input{
	width: 100%;
	padding-right: 15px;
}
.create-button{
	display: flex;
	justify-content: center;
	align-items: center;
	flex-shrink: 0;
	width: 60px;
	height: 30px;
	border-radius: 10px;
	font-size: 14px;
	color:#279abf;
	background-color: #cfebfb;
	box-shadow: 0 0 2px 2px rgba(0,0,0,0.1);
}
.create-content:after{
	content: '';
	position: absolute;
	display: block;
	right: 0;
	left: 0;
	bottom: -12px;
	width: 0;
	height: 0;
	border: 13px solid red;
	border-top-color: #F8F8F8;
	border-bottom: none;
	border-left-color: transparent;
	border-right-color: transparent;
	box-shadow: 1px 1px 2px 2px rgba(0,0,0) inset;	
	margin: 0 auto;
	z-index: -1;
}
.defaault_onfo{
	text-align: center;
	font-size: 12px;
	padding-top: 20px;
	color: #999999;
}
.icon-jia{
	transition: transform 0.3s;
}
.create-todo-active{
	transform: rotate(135deg);
}
.create-show{
	opacity: 1;
	transform: scale(1) translateY(0);
}
</style>
