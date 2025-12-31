<template>
  <div class="tools-container">
    <header>
      <h1>MHWS巨戟龙武器工具</h1>
    </header>
    <div class="part">
      <div class="name">机械素材生产编号</div>
      <div class="content">
        <el-select v-model="productionNumberValue.one" placeholder="Select" style="width: 240px">
          <el-option
            v-for="item in productionNumberOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <el-select v-model="productionNumberValue.two" placeholder="Select" style="width: 240px">
          <el-option
            v-for="item in productionNumberOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
        <el-select v-model="productionNumberValue.three" placeholder="Select" style="width: 240px">
          <el-option
            v-for="item in productionNumberOptions"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </el-select>
      </div>
    </div>
    <div class="part">
      <div class="name">系列技能+组合技能</div>
      <div class="content">
        <el-select v-model="seriesOfSkillsValue" placeholder="Select" style="width: 240px">
          <el-option
            v-for="item in seriesOfSkillsOptions"
            :key="`seriesOfSkillsOptions${item.id}`"
            :label="item.name"
            :value="item.id"
          />
        </el-select>
        <el-select v-model="combinedSkillsValue" placeholder="Select" style="width: 240px">
          <el-option
            v-for="item in combinedSkillsOptions"
            :key="`combinedSkillsOptions${item.id}`"
            :label="item.name"
            :value="item.id"
          />
        </el-select>
      </div>
    </div>
    <div class="part">
      <div class="name">
        CE-编号
        <span>（点击下面编号复制）</span>
      </div>
      <div class="content ceNumber">
        <p @click="copyToClipboard(item)">{{ CENumber }}</p>
        <!-- 复制成功提示 -->
        <transition name="fade">
          <div v-if="showSuccess" class="success-message">复制成功！</div>
        </transition>
      </div>
    </div>
    <div class="part">
      <div class="name">
        复原加成
        <span>
          （从下往上，每级3位数，末尾补0，最一位2位数，10一下为09。同一个属性EX最多俩个。例：17017016016009）
        </span>
      </div>
      <div class="content recoveryBonus">
        <p v-for="item in recoveryBonusOptions.slice(0, 4)" :key="item.id">
          {{ item.name + item.type + ': ' + item.value }}
        </p>
        <p v-for="item in recoveryBonusOptions.slice(4, 6)" :key="item.id">
          {{ item.name + item.type + ': ' + item.value }}
        </p>
        <p v-for="item in recoveryBonusOptions.slice(6, 7)" :key="item.id">
          {{ item.name + item.type + ': ' + item.value }}
        </p>
        <p v-for="item in recoveryBonusOptions.slice(7, 8)" :key="item.id">
          {{ item.name + item.type + ': ' + item.value }}
        </p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import mhws_ce_data from '@/assets/mhws_ce_data.js'

// 机械素材生产编号
const productionNumberValue = ref({
  one: 1,
  two: 1,
  three: 1,
})
const productionNumberOptions = ref([
  {
    value: 1,
    label: '攻击',
  },
  {
    value: 2,
    label: '会心',
  },
])
// 系列技能
const seriesOfSkillsValue = ref(1)
const seriesOfSkillsOptions = ref(mhws_ce_data.seriesOfSkills || [])

// 组合技能
const combinedSkillsValue = ref(1)
const combinedSkillsOptions = ref(mhws_ce_data.combinedSkills || [])

const recoveryBonusOptions = ref(mhws_ce_data.recoveryBonus || [])

// CE-编号
// 生产编号规则：攻击为001，会心为002
// 技能公式：seriesOfSkillsValue * 15 + combinedSkillsValue + 2
// CENumber 三个基底 [生产编号规则.one, 生产编号规则.two, 生产编号规则.three]
const CENumber = computed(() => {
  // 计算技能值
  const scValue = seriesOfSkillsValue.value * 15 + combinedSkillsValue.value + 2

  // 解构生产编号
  const { one, two, three } = productionNumberValue.value

  // 生成三位生产编号，每位由 (scValue 对应位 * 10) + 对应编号 组成
  const productionNumberValues = [
    (Math.floor(scValue / 100) % 10) * 10 + one,
    (Math.floor(scValue / 10) % 10) * 10 + two,
    (scValue % 10) * 10 + three,
  ]

  // 拼接为 9 位字符串，不足 3 位补 0
  return productionNumberValues.map((v) => String(v).padStart(3, '0')).join('')
})

// 示例数据
const showSuccess = ref(false)
// 复制到剪贴板函数
const copyToClipboard = async (text) => {
  try {
    await navigator.clipboard.writeText(text)
    showSuccess.value = true

    // 2秒后隐藏提示
    setTimeout(() => {
      showSuccess.value = false
    }, 2000)
  } catch (err) {
    console.error('复制失败:', err)
  }
}
</script>

<style scoped>
.tools-container .el-select {
  margin-right: 10px;
}
.tools-container header {
  width: 100%;
  text-align: center;
  font-weight: bold;
  font-size: 24px;
  margin-bottom: 20px;
}
.tools-container .content {
  padding: 20px 10px 20px 0px;
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}
.tools-container .content {
  padding: 20px 10px 20px 0px;
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}
.tools-container .content.ceNumber {
  display: block;
  cursor: pointer;
}
.tools-container .content.ceNumber p {
  background-color: #f0f0f0;
  padding: 5px 10px;
  border-radius: 5px;
}

.tools-container .name {
  font-size: 22px;
  font-weight: bold;
}
.tools-container .name span {
  font-size: 14px;
  color: red;
}
.tools-container .content.recoveryBonus p {
  width: 130px;
  padding-right: 20px;
}

.copyable-paragraph {
  cursor: pointer;
  padding: 10px;
  border: 1px solid #eee;
  border-radius: 4px;
  transition: all 0.3s;
}

.copyable-paragraph:hover {
  background-color: #f5f5f5;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

.success-message {
  position: fixed;
  top: 20px;
  right: 20px;
  padding: 10px 20px;
  background-color: #4caf50;
  color: white;
  border-radius: 4px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  z-index: 1000;
}

/* 过渡动画 */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
