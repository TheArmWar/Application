<script setup>
import Button from './button/button.vue'

import release_icon from '@/assets/release_icon.png';
import up_icon from '@/assets/up_icon.png';
import press_icon from '@/assets/press_icon.png';
import left_icon from '@/assets/left_icon.png';
import down_icon from '@/assets/down_icon.png';
import right_icon from '@/assets/right_icon.png';
import set_default_icon from '@/assets/set_default_icon.png';
import stop_icon from '@/assets/stop_icon.png';
import reset_icon from '@/assets/reset_icon.png';

import { ref } from 'vue'
import { getCurrentInstance } from 'vue'
const { emit } = getCurrentInstance()

const handleButtonClick = (buttonName, image) => {
  emit('button-clicked-parent', buttonName, image);
}
</script>

<template>
  <div class="commands">
    <h1>Commands</h1>
    <div class="button-holder">
      <Button :image="release_icon" @button-clicked="handleButtonClick" buttonName="release" />
      <Button :image="up_icon" @button-clicked="handleButtonClick" buttonName="up" />
      <Button :image="press_icon" @button-clicked="handleButtonClick" buttonName="press" />

      <Button :image="left_icon" @button-clicked="handleButtonClick" buttonName="left" />
      <Button :image="down_icon" @button-clicked="handleButtonClick" buttonName="down" />
      <Button :image="right_icon" @button-clicked="handleButtonClick" buttonName="right" />

      <div class="button-holder other-holder">
        <Button other=true :image="set_default_icon" @button-clicked="" buttonName=" set-zero" />
        <Button other=true :image="stop_icon" @button-clicked="" buttonName="stop" />
        <Button other=true :image="reset_icon" @button-clicked="" buttonName="go-to-zero" />
      </div>
    </div>
  </div>
</template>

<style scoped>
.commands {
  width: 100%;
}

.button-holder {
  display: grid;
  grid-template-columns: 30% 30% 30%;
  gap: 5%;
  width: calc(96px *3 - 8px);
  margin: auto;
}

h1 {
  color: var(--white-text);
  font-weight: 600;
}

.other-holder {
  margin-top: 30px;
}


h1 {
  color: var(--white-text);
  font-weight: 400;
  margin-bottom: 40px;
  text-align: center;
}
</style>
