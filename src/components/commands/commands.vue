<script setup>
import Button from './button/button.vue'

import rotate_cw_icon from '@/assets/rotate_cw_icon.png';
import rotate_ccw_icon from '@/assets/rotate_ccw_icon.png';
import release_icon from '@/assets/release_icon.png';
import forward_icon from '@/assets/forward_icon.png';
import backward_icon from '@/assets/backward_icon.png';
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
      <Button :image="release_icon" @button-clicked="handleButtonClick"
        buttonName="release" />
      <Button :image="up_icon" @button-clicked="handleButtonClick"
        buttonName="up" />
      <Button :image="press_icon" @button-clicked="handleButtonClick"
        buttonName="press" />

      <Button :image="rotate_ccw_icon" @button-clicked="handleButtonClick"
        buttonName="rotate_ccw" />
      <Button :image="forward_icon" @button-clicked="handleButtonClick"
        buttonName="forward" />
      <Button :image="rotate_cw_icon" @button-clicked="handleButtonClick"
        buttonName="rotate_cw" />

      <Button :image="left_icon" @button-clicked="handleButtonClick"
        buttonName="left" />
      <Button :image="backward_icon" @button-clicked="handleButtonClick"
        buttonName="backward" />
      <Button :image="right_icon" @button-clicked="handleButtonClick"
        buttonName="right" />

      <Button :image="set_default_icon" @button-clicked="handleButtonClick"
        buttonName="set-zero" />
      <Button :image="down_icon" @button-clicked="handleButtonClick"
        buttonName="down" />
      <Button :image="reset_icon" @button-clicked="handleButtonClick"
        buttonName="go-to_zero" />

      <div class="button-holder other-holder">
        <Button other=true :image="stop_icon" @button-clicked="handleButtonClick"
          buttonName="stop" />
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
  grid-column: 2/3;
}


h1 {
  color: var(--white-text);
  font-weight: 400;
  margin-bottom: 40px;
  text-align: center;
}
</style>
