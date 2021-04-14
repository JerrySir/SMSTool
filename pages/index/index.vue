<template>
	<view class="wrap">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<view class="box-item">
				<view class="label-item">姓名：</view>
				<input type="text" placeholder="李女士" v-model="name" />
			</view>
			<view class="box-item">
				<view class="label-item">手机号：</view>
				<input type="number" placeholder="手机号" v-model="to" />
			</view>
			<view class="box-textarea">
				<textarea v-model="body" style="flex: 1;" @blur="onSaveTemplate()" />
			</view>
			<view class="box-btns">
				<button type="default" @click="onReset()" class="btn-send">重置</button>
				<button type="default" @click="onSend()" class="btn-send">发送</button>
			</view>
		</view>
		<!-- 历史 -->
		<view class="label-history-title">历史记录</view>
		<view class="box-history-item" v-for="(item, index) in listHistory" :key="index">
			<view class="label-history-item" @click="onSelectHistoryItem(index)">
				{{ item.name }} {{ item.to }} {{ item.body }}
			</view>
			<button type="warn" @click="onDeleteHistoryItem(index)">删</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				to: '',
				body: '多多买菜有您的订单，有时间来5号楼取。',
				listHistory: []
			}
		},
		onLoad() {
			this.onGetTemplate()
			this.getHistory()
		},
		methods: {
			onSend() {
				var msg = plus.messaging.createMessage(plus.messaging.TYPE_SMS)
				msg.to = [this.to]
				msg.body = this.name + ' 您好：' + this.body
				plus.messaging.sendMessage(msg)
				
				// this.listHistory.unshift({
				// 	name: this.name,
				// 	to: this.to,
				// 	body: this.body
				// })
				this.listHistory = [{
					name: this.name,
					to: this.to,
					body: this.body
				}, ...this.listHistory]
				this.onSave()
			},
			onReset() {
				this.name = ''
				this.to = ''
				this.body = ''
			},
			onSelectHistoryItem(index) {
				const { name, to, body } = this.listHistory[index]
				this.name = name
				this.to = to
				this.body = body
			},
			onDeleteHistoryItem(index) {
				this.listHistory.splice(index,1)
				this.onSave()
			},
			onSave() {
				uni.setStorageSync('history', this.listHistory)
			},
			getHistory() {
				this.listHistory = uni.getStorageSync('history')
			},
			onSaveTemplate() {
				uni.setStorageSync('template', this.body)
			},
			onGetTemplate() {
				this.body = uni.getStorageSync('template')
			},
		}
	}
</script>

<style>
	.wrap {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.content {
		display: flex;
		flex-direction: column;
		/* justify-content: center; */
		align-items: center;
	}
	
	.box-item {
		margin: 30rpx 0;
		display: flex;
		flex-direction: row;
		align-items: center;
		border: 1rpx solid #D3D3D3;
	}
	
	.label-item {
		width: 130rpx;
	}
	
	.box-textarea {
		height: 200rpx;
		border: 1rpx solid #D3D3D3;
	}
	
	.box-btns {
		width: 100%;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}
	
	.btn-send {
		margin-top: 30rpx;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
	
	.label-history-title {
		margin-top: 30rpx;
	}
	.box-history-item {
		width: 100%;
		display: flex;
		flex-direction: row;
		justify-content: space-between;
		align-items: center;
	}
	.label-history-item {
	}
</style>
