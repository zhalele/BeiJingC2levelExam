<template>
  <div v-if="list && !isEnd">
    <div class="mb-4">
      <h3 style="text-align: center;">北京安全员(C2)考核</h3>
    </div>
    <el-alert :title="`题目总数为${total}题，每次随机抽取100道题`" type="info" :closable="false" />
    <div class="sub-title ">当前题目: <span class="weight">{{ count + 1 }}/{{ list.length }}</span> </div>
    <div style="display: flex; align-items: center; justify-content: center;">
      <el-button size="large" type="success" round @click="changeO" :icon="ArrowLeft">上一题</el-button>
      <el-button size="large" type="success" round @click="changeB" :icon="ArrowRight">下一题</el-button>
    </div>
    <el-tag style="margin-top: 4px;" v-if="list[count].type == '单选题'" type="warning" size="">{{ list[count].type
      }}</el-tag>
    <el-tag style="margin-top: 4px;" type="info" v-if="list[count].type == '多选题'" size="">{{ list[count].type
      }}</el-tag>
    <el-tag style="margin-top: 4px;" type="success" v-if="list[count].type == '判断题'" size="">{{ list[count].type
      }}</el-tag>
    <p style="font-weight: bold; ">{{ count + 1 }}、{{ list[count].title }}</p>
    <div style="margin: 10px 0 ;">你选择的答案：<span class="weight"> {{ list[count].option.answer }}</span></div>
    <!-- <el-alert :title="`正确答案：${list[count].trueAnswer}`" type="success" :closable="false" /> -->

    <!-- 单选题,判断题题目  -->
    <el-radio-group v-model="list[count].option.answer" v-if="list[count].type == '单选题' || list[count].type == '判断题'"
      class="group-style">
      <div v-for="i in list[count].option.name">
        <el-radio size="large" label="" :value="i.split('.')[0]">
          <div class="radio-label ">
            {{ i }}</div>
        </el-radio>
      </div>
    </el-radio-group>
    <!-- 多选题题目  -->
    <el-checkbox-group v-model="list[count].option.answer" v-if="list[count].type == '多选题'">
      <div v-for="i in list[count].option.name">
        <el-checkbox size="large" label="" :value="i.split('.')[0]">
          <span class="radio-label"> {{ i }}</span>
        </el-checkbox>
      </div>
    </el-checkbox-group>
    <div><el-button round type="info" size="large" style="display: block;  margin: 0 auto;" @click="submit"
        :icon="Check">提交试卷</el-button>
    </div>

  </div>
  <!-- 考试结束流程 -->
  <div v-if="isEnd">
    <div class="end-style">
      最终分数<div style="color: #67c23a; text-align: center; font-size: 50px; font-weight: bold;">{{ endScore }}</div>
    </div>
    <div class="end-style" style="margin: 5px 0;">
      以下为错题记录
    </div>
    <div v-if="errList">
      <div v-for="item in errList" class="box">
        <p style="font-weight: bold; font-size: 17px">{{ item.title }}</p>
        <el-alert :title="`你选择的答案：${item.option.answer}`" type="error" :closable="false" />
        <div style="height: 5px;"></div>
        <el-alert :title="`正确答案：${item.trueAnswer}`" type="success" :closable="false" />
        <!-- 单选题  ，判断题 -->
        <el-radio-group class="group-style" v-model="item.option.answer" disabled
          v-if="item.type == '单选题' || item.type == '判断题'">
          <div v-for="i in item.option.name">
            <div><el-radio size="large" :value="i.split('.')[0]">
                <div style=" font-size: 16px;">{{ i }} </div>
              </el-radio></div>
          </div>
        </el-radio-group>


        <!-- 多选题  -->
        <el-checkbox-group v-model="item.option.answer" v-if="item.type == '多选题'" disabled>
          <div v-for="i in item.option.name">
            <el-checkbox size="large" label="" :value="i.split('.')[0]">
              <span class="radio-label">
                {{ i }}</span>
            </el-checkbox>
          </div>
        </el-checkbox-group>
      </div>
    </div>
  </div>

</template>


<script setup lang="ts">
import {
  ArrowLeft,
  ArrowRight,
  Check
} from '@element-plus/icons-vue'
import { ref, onMounted } from "vue";
import { ElMessage } from "element-plus";
import { List } from './problem'
import { DXList } from './duoxuan';
// import { List } from './test'
defineProps<{ msg: string }>();

const count = ref(0);
const total = ref(0);
const list = ref();
const errList = ref(); //错题记录
// 是否已经结束答题
const isEnd = ref(false);
const endScore = ref(0);


const toast = (item: any) => {
  console.log(item)

  if (item.option.answer === item.trueAnswer) {
    ElMessage.success("答案正确");
  } else {
    ElMessage.error("答案错误");

  }
};
onMounted(() => {
  errList.value = []

  const questionsCopy = [...List, ...DXList];
  console.log('所有题目：', List.concat(DXList))
  total.value = List.concat(DXList).length
  // const questionsCopy = [...List];
  // console.log('所有题目：', List)
  // total.value = List.length
  // 抽取题目 
  pickRandomQuestions(questionsCopy, 100)
  // console.log(list.value)
});


function isFieldEmpty(value: any) {
  return (
    value === null ||
    value === undefined ||
    (Array.isArray(value) && value.length === 0) ||
    (typeof value === 'string' && value.trim() === '')
  );
}


const pickRandomQuestions = (questions: any[], count: number) => {
  const picked = []; // 存储随机选取的题目
  while (count > picked.length) {
    // 随机选择一个索引
    const index = Math.floor(Math.random() * questions.length);
    // 将选中的题目添加到结果数组
    picked.push(questions[index]);
    // 从原始数组中移除选中的题目，避免重复
    questions.splice(index, 1);
  }
  console.log('抽取的题目：', picked)
  list.value = picked
};


function changeO() {
  if (count.value === 0) {
    return
  }
  count.value--

}
function changeB() {
  if (count.value === list.value.length - 1) {
    return
  }
  if (list.value[count.value].option.answer == '' || (Array.isArray(list.value[count.value].option.answer) && list.value[count.value].option.answer.length === 0)) {
    ElMessage.warning("请先回答当前题目!");
    return
  }
  count.value++

}
function submit() {
  let unanswered: any[] = [];

  console.log(list.value)

  list.value.forEach((question: any, index: any) => {
    // if (!question.option.answer || question.option.answer.trim() === "") 
    if (isFieldEmpty(question.option.answer)) {
      // 如果答案未定义、为空或仅包含空白字符，则认为该题目未答  
      unanswered.push(index + 1); // 题目编号从1开始，所以加1  
    }
  });
  if (unanswered.length > 0) {
    // 如果有未答的题目，显示提示信息  
    // console.log("有题目未答，具体题目编号如下：", unanswered.join(", "));
    ElMessage.error(`有题目未答,具体题目编号如下: ${unanswered.join(", ")}`);
  } else {

    console.log("所有题目都已回答。");
    checkAnswers()
  }
}

function checkAnswers() {
  const errors: any[] = [];
  let score = 0;
  list.value.forEach((question: any) => {
    let providedAnswer = question.option.answer;
    let trueAnswer = question.trueAnswer;

    // 多选题答案处理
    if (Array.isArray(providedAnswer)) {
      providedAnswer = providedAnswer.map(answer => answer.toString().trim()).sort().join('');
      trueAnswer = trueAnswer.toString().trim();
    } else {
      // 单选题答案处理
      providedAnswer = providedAnswer.toString().trim();
      trueAnswer = trueAnswer.toString().trim();
    }
    // console.log('你选择的答案', providedAnswer)
    // console.log('正确答案', trueAnswer)

    if (providedAnswer !== trueAnswer) {
      // 如果答案不一致，将错误信息存储到errors数组中  
      errors.push(question);
    } else {
      score++;
    }
  });

  isEnd.value = true
  endScore.value = score
  errList.value = errors
  console.log(errors)
  ElMessage.success(`你的最终分数：${score}`);
}

</script>


<style>
.box {}

.weight {
  font-weight: bold;
}

.ep-radio-group {
  line-height: inherit;

}

.ep-radio.ep-radio--large {
  height: auto;
  min-height: 65px;
}


.ep-radio__label {
  display: block;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: normal;
  word-break: break-all;
}


.ep-radio__input.is-disabled+span.ep-radio__label {
  color: #686c76;
}


.radio-label {
  font-size: 15px;
}

.group-style {
  display: flex;
  flex-flow: column nowrap;
  align-items: flex-start;
  width: 100%;
}

.ep-checkbox.ep-checkbox--large {
  height: auto;
  min-height: 65px;
}

.ep-checkbox__label {
  line-height: normal;
  display: block;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: normal;
  word-break: break-all;

}

.ep-checkbox__input.is-disabled+span.ep-checkbox__label {
  color: #686c76;
}




.sub-title {
  text-align: center;
  margin: 6px 0;
}

.end-style {
  text-align: center;
  font-size: 20px;
  font-weight: bold;
}
</style>
