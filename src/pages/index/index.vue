<template>
  <view class="index">
    <nut-menu active-color="#FFD54F">
      <nut-menu-item v-model="state.currentType" :options="state.type" @change="handleChangeType"/>
      <nut-menu-item v-model="state.currentGame" @change="handleChangeGame" :options="state.game"/>
    </nut-menu>
    <view class="content">
      <nut-collapse v-model:active="state.activeName" :accordion="true" icon="down-arrow" title-icon="star"
                    title-icon-color="#FFD54F">
        <nut-collapse-item v-for="(item,index) in state.list" :key="index" :title="`${item.name} (${item.rankStr})`"
                           :name="item.id">
          <template v-for="(smith, index) in state.gunSmith" :key="index">
            <template v-if="smith.value === 'comment' || smith.value === 'rangeStr'">
              <view v-if="item[smith.value]">
                <view class="text">{{ smith.text }}</view>
                : {{ item[smith.value] }}
              </view>
            </template>
            <template v-else-if="smith.value === 'updateTime'">
              <view v-if="item[smith.value]">
                <view class="text">{{ smith.text }}</view>
                : {{ getDate(item[smith.value]) }}
              </view>
            </template>
            <template v-else>
              <view v-if="item[smith.value] && item[smith.value].name !== ''">
                <view class="text">{{ smith.text }}</view>
                :
                {{ item[smith.value].name }}
                {{
                  item[smith.value].tune ? `【${item[smith.value].tune.left === '' ? ' - ' : item[smith.value].tune.left} | ${item[smith.value].tune.down === '' ? ' - ' : item[smith.value].tune.down}】` : ''
                }}
              </view>
            </template>
          </template>
        </nut-collapse-item>
      </nut-collapse>
    </view>
  </view>
</template>

<script setup>
import {reactive} from 'vue';
import {getData} from "../../api/api";
import Taro from "@tarojs/taro";

const state = reactive({
  type: [
    {text: '突击步枪', value: 'AR'},
    {text: '战斗步枪', value: 'BR'},
    {text: '射手步枪', value: 'MR'},
    {text: '冲锋枪', value: 'SMG'},
    {text: '轻机枪', value: 'LMG'},
    {text: '狙击枪', value: 'Sniper'},
    {text: '手枪', value: 'Pistol'},
    {text: '霰弹枪', value: 'Shotgun'}
  ],
  game: [
    {text: '战区2.0', value: 'wz2_gunsmith'},
    {text: '多人游戏', value: 'mwii_gunsmith'},
  ],
  gunSmith: [
    {
      text: '适用距离',
      value: 'rangeStr'
    },
    {
      text: '枪口',
      value: 'muzzle',
    },
    {
      text: '枪管',
      value: 'barrel',
    },
    {
      text: '镭射',
      value: 'laser',
    },
    {
      text: '瞄具',
      value: 'optic',
    },
    {
      text: '枪托',
      value: 'stock',
    },
    {
      text: '下挂',
      value: 'underbarrel',
    },
    {
      text: '弹夹',
      value: 'magazine',
    },
    {
      text: '弹药',
      value: 'ammunition',
    },
    {
      text: '后握把',
      value: 'rearGrip',
    },
    {
      text: '腮托',
      value: 'comb',
    },
    {
      text: '枪机',
      value: 'bolt',
    },
    {
      text: '护木',
      value: 'guard',
    },
    {
      text: '备注',
      value: 'comment',
    },
    {
      text: '更新时间',
      value: 'updateTime',
    },
  ],
  currentType: 'AR',
  currentGame: 'wz2_gunsmith',
  activeName: 0,
  data: [],
  list: [],
});

const getDate = (timestamp) => {
  const date = new Date(timestamp);
  const y = date.getFullYear();
  const m = date.getMonth() + 1;
  const d = date.getDate();
  return `${y}-${m < 10 ? '0' + m : m}-${d < 10 ? '0' + d : d}`;
}
const handleChangeType = type => {
  state.activeName = 0;
  state.list = state.data.filter(item => item.type === type);
}

const handleChangeGame = async (game) => {
  Taro.showLoading();
  const {data: {data: {list}}} = await getData(game);
  state.data = list;
  handleChangeType(state.currentType);
  Taro.hideLoading();
}

handleChangeGame(state.currentGame);

</script>

<style lang="scss">
.content {
  margin-top: 3%;
}

.text {
  display: inline;
  font-weight: bold;
}
</style>
