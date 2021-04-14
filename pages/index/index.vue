<template>
	<view class="wrap">
    <view class="label-app-title">Hi，你好</view>
    <view class="box-action">
      <view class="box-input">
        <input type="text" placeholder="姓名输入" v-model="name" style="flex: 1;" />
      </view>
      <view class="box-input">
        <input type="number" placeholder="手机号" v-model="to" style="flex: 1;" />
      </view>
      <view class="box-text-area">
        <textarea v-model="body" placeholder="短信内容" style="flex: 1;" />
      </view>
      <view class="btn-action send" @click="onSend()">发送</view>
      <view class="flex-row-between flex-align-center" style="width: 100%;">
        <view class="btn-action save" @click="onSaveTemplate()">保存模板</view>
        <view class="btn-action reset" @click="onReset()">重置</view>
      </view>
    </view>
		<!-- 历史 -->
    <view class="box-history">
      <view class="box-top">
        <view class="label-action" :class="historyType==1&&'active'" @click="historyType=1">内容模板</view>
        <view class="label-action" style="margin: 0 10rpx;">|</view>
        <view class="label-action" :class="historyType==0&&'active'" @click="historyType=0">历史记录</view>
        <view class="flex-1"></view>
        <image src="../../static/icon-delete.png" mode="" class="icon-delete" @click="onDeleteAllHistory()"></image>
        <view class="label-action delete" @click="onDeleteAllHistory()">全部清除</view>
      </view>
      <view class="line-top"></view>
      <view  v-if="historyType===0">
        <view class="box-history-item" v-for="(item, index) in listHistory" :key="index">
          <view class="box-content">
            <view class="label-top">
              {{ item.name }} {{ item.to }}
            </view>
            <view class="label-bottom">
              {{ item.body }}
            </view>
          </view>
          <view class="btn-action send" @click="onSelectHistoryItem(index)">发</view>
          <view class="btn-action delete" @click="onDeleteHistoryItem(index)">删</view>
        </view>
      </view>
      <!-- 模板 -->
      <view v-if="historyType===1">
        <view class="box-history-item" v-for="(item, index) in listTemplate" :key="index">
          <view class="box-content">
            <view class="label-top">
              {{ item }}
            </view>
          </view>
          <view class="btn-action send" @click="body=item">发</view>
          <view class="btn-action delete" @click="onDeleteTemplate(index)">删</view>
        </view>
      </view>
    </view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				name: '',
				to: '',
				body: '',
				listHistory: [],
        listTemplate: [],
        historyType: 1, // 0历史记录 1模板
			}
		},
		onLoad() {
			this.onGetTemplateList()
			this.getHistory()
		},
		methods: {
			onSend() {
        if (this.name.length <= 0) {
          uni.showToast({
            icon: 'none',
            title: '请输入姓名'
          })
          return
        }
        if (this.to.length <= 0) {
          uni.showToast({
            icon: 'none',
            title: '请输入手机号'
          })
          return
        }
        if (this.body.length <= 0) {
          uni.showToast({
            icon: 'none',
            title: '请输入短信内容'
          })
          return
        }
        
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
      onSaveTemplate() {
        if (this.body.length <= 0) {
          uni.showToast({
            icon: 'none',
            title: '请输入短信内容'
          })
          return
        }
        this.listTemplate = [this.body, ...this.listTemplate]
        this.onSaveTemplateList()
        uni.showToast({
          title: '已保存为模板'
        })
      },
      onDeleteTemplate(index) {
        this.listTemplate.splice(index,1)
        this.onSaveTemplateList()
      },
      onSaveTemplateList() {
        uni.setStorageSync('templateList', this.listTemplate)
      },
      onGetTemplateList() {
      	this.listTemplate = uni.getStorageSync('templateList')
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
      onDeleteAllHistory() {
        if (this.historyType === 0) {
          this.listHistory = []
          this.onSave()
        } else {
          this.listTemplate = []
          this.onSaveTemplateList()
        }
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
			// onSaveTemplate() {
			// 	uni.setStorageSync('template', this.body)
			// },
			// onGetTemplate() {
			// 	this.body = uni.getStorageSync('template')
			// },
		}
	}
</script>

<style lang="scss" scoped>
	.wrap {
    @extend .flex-column;
    @extend .flex-align-center;
    width: 750rpx;
    // height: 100vh;
    min-height: 100vh;
    background: linear-gradient(90deg, #8FB6FE, #5177F5);
	}
  
  .label-app-title {
    margin-top: 136rpx;
    height: 36rpx;
    font-size: 36rpx;
    font-family: Source Han Sans SC;
    font-weight: 500;
    color: #FFFFFF;
    line-height: 36rpx;
  }
  
  .box-action {
    @extend .flex-column;
    @extend .flex-align-center;
    margin-top: 73rpx;
    width: 690rpx;
    height: 800rpx;
    background: #FFFFFF;
    border-radius: 40rpx;
    padding: 20rpx 40rpx;
    box-sizing: border-box;
    .box-input {
      @extend .flex-row;
      @extend .flex-align-center;
      margin-top: 30rpx;
      width: 610rpx;
      height: 80rpx;
      background: #F2F2FC;
      border-radius: 20rpx;
      padding: 0 20rpx;
      box-sizing: border-box;
    }
    .box-text-area {
      margin: 30rpx 0;
      width: 610rpx;
      height: 250rpx;
      background: #F2F2FC;
      border-radius: 20rpx;
      padding: 20rpx;
      box-sizing: border-box;
    }
    .btn-action {
      &.send {
        background: linear-gradient(90deg, #70A3FF, #3271FF);
      }
      &.save {
        background: #53B970;
        width: 300rpx;
      }
      &.reset {
        background: #B7BAC0;
        width: 300rpx;
      }
      margin-top: 20rpx;
      width: 610rpx;
      height: 80rpx;
      border-radius: 20rpx;
      font-size: 36rpx;
      font-family: Source Han Sans SC;
      font-weight: 400;
      color: #FFFFFF;
      line-height: 80rpx;
      text-align: center;
    }
  }
  
  .box-history {
    @extend .flex-column;
    @extend .flex-align-center;
    margin: 44rpx 68rpx 200rpx;
    width: calc(100% - 136rpx);
    .box-top {
      @extend .flex-row-between;
      @extend .flex-align-center;
      width: 100%;
      .label-action {
        &.delete {
          color: #C0D5FF;
        }
        &.active {
          color: #FFFFFF;
          font-size: 30rpx;
        }
        height: 27rpx;
        font-size: 28rpx;
        font-family: Source Han Sans SC;
        font-weight: 400;
        color: #C0D5FF;
        line-height: 27rpx;
      }
      .icon-delete {
        margin: 0 6rpx;
        width: 26rpx;
        height: 28rpx;
      }
    }
    .line-top {
      margin-top: 19rpx;
      width: 614rpx;
      height: 2rpx;
      background: #EEEEEE;
      border-radius: 50%;
    }
    .box-history-item {
      @extend .flex-row;
      @extend .flex-align-center;
      margin-top: 26rpx;
      width: 609rpx;
      height: 62rpx;
      .box-content {
        @extend .flex-column-between;
        flex: 1;
        margin-right: 3rpx;
        .label-top {
          height: 24rpx;
          font-size: 24rpx;
          font-family: Source Han Sans SC;
          font-weight: 400;
          color: #FFFFFF;
          line-height: 24rpx;
        }
        .label-bottom {
          margin-top: 13rpx;
          height: 24rpx;
          font-size: 24rpx;
          font-family: Source Han Sans SC;
          font-weight: 400;
          color: #C0D5FF;
          line-height: 24rpx;
        }
      }
      .btn-action {
        &.send {
          background: #53B970;
        }
        &.delete {
          background: #E85252;
        }
        margin-left: 15rpx;
        width: 60rpx;
        height: 60rpx;
        color: #FFFFFF;
        border-radius: 10rpx;
        font-size: 28rpx;
        font-family: Source Han Sans SC;
        font-weight: 400;
        color: #FFFFFF;
        line-height: 60rpx;
        text-align: center;
      }
    }
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
