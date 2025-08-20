<template>
  <view class="container">
    <!-- 右上角加号 -->
    <view class="header">
      <button class="add-btn" @click="showAddDice = true">＋</button>
    </view>

    <!-- 骰子展示区 -->
    <view class="dice-grid">
      <view 
        class="dice-item" 
        v-for="(dice, index) in diceList" 
        :key="index"
        @longpress="onDiceLongPress(dice, index)"
      >
        <text class="dice-id">{{ dice.id }}</text>
        <text class="dice-value">{{ dice.value }}</text>
      </view>
    </view>

    <!-- 底部投掷按钮 -->
    <button class="roll-btn" @click="rollAllDice">投掷</button>

    <!-- 添加骰子弹窗 -->
    <view v-if="showAddDice" class="dialog-mask">
      <view class="dialog">
        <input v-model="newDice.id" placeholder="骰子编号 (如 A, B, C)" />
        <input v-model.number="newDice.faces" placeholder="骰子面数 (如 6)" type="number" />
        <view class="dialog-actions">
          <button @click="addDice">确定</button>
          <button @click="showAddDice=false">取消</button>
        </view>
      </view>
    </view>

    <!-- 编辑/删除弹窗 -->
    <view v-if="showEditDice" class="dialog-mask">
      <view class="dialog">
        <button @click="editDice">编辑</button>
        <button @click="deleteDice">删除</button>
        <button @click="showEditDice=false">取消</button>
      </view>
    </view>
  </view>
</template>

<script setup>
import { ref } from 'vue'

const diceList = ref([])
const showAddDice = ref(false)
const showEditDice = ref(false)
const newDice = ref({ id: '', faces: 6 })
let currentDiceIndex = null

// 添加骰子
const addDice = () => {
  if (!newDice.value.id) newDice.value.id = '?'
  if (!newDice.value.faces || newDice.value.faces < 2) newDice.value.faces = 6

  diceList.value.push({
    id: newDice.value.id,
    faces: newDice.value.faces,
    value: 1
  })

  newDice.value = { id: '', faces: 6 }
  showAddDice.value = false
}

// 长按骰子
const onDiceLongPress = (dice, index) => {
  currentDiceIndex = index
  showEditDice.value = true
}

// 编辑骰子
const editDice = () => {
  const d = diceList.value[currentDiceIndex]
  newDice.value = { ...d }
  diceList.value.splice(currentDiceIndex, 1) // 移除旧的
  showAddDice.value = true
  showEditDice.value = false
}

// 删除骰子
const deleteDice = () => {
  diceList.value.splice(currentDiceIndex, 1)
  showEditDice.value = false
}

// 投掷动画
const rollAllDice = () => {
  const interval = setInterval(() => {
    diceList.value.forEach(d => {
      d.value = Math.floor(Math.random() * d.faces) + 1
    })
  }, 30)

  setTimeout(() => clearInterval(interval), 1000) // 1 秒后停止
}
</script>

<style>
.container {
  flex: 1;
  padding: 20rpx;
  display: flex;
  flex-direction: column;
}

.header {
  display: flex;
  justify-content: flex-end;
}

/* .add-btn {
  font-size: 40rpx;
  color: purple;
} */

.add-btn {
  font-size: 50rpx;
  color: purple;
  background: transparent;
  border: none;
  position: fixed;
  top: calc(var(--status-bar-height) + 110rpx);
  right: 40rpx;
  height: 66px;
}



.dice-grid {
  flex: 1;
  flex-wrap: wrap;
  display: flex;
  justify-content: flex-start;
}

.dice-item {
  width: 18%;
  margin: 1%;
  padding: 20rpx;
  background: #f2f2f2;
  border-radius: 12rpx;
  align-items: center;
  justify-content: center;
  display: flex;
  flex-direction: column;
}

.dice-id {
  font-size: 28rpx;
  font-weight: bold;
  color: purple;
}

.dice-value {
  font-size: 36rpx;
  margin-top: 10rpx;
}

.roll-btn {
  background: green;
  color: #fff;
  position: fixed;
  bottom: 40rpx;
  left: 50%;
  transform: translateX(-50%);
  width: 200rpx;
  height: 80rpx;
  line-height: 80rpx;
  border-radius: 12rpx;
  text-align: center;
}


.dialog-mask {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0,0,0,0.5);
  justify-content: center;
  align-items: center;
  display: flex;
}

.dialog {
  background: #fff;
  padding: 20rpx;
  border-radius: 12rpx;
  width: 70%;
}

.dialog-actions {
  display: flex;
  justify-content: space-around;
  margin-top: 20rpx;
}
</style>
