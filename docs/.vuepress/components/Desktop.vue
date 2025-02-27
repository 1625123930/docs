<script setup lang="ts">
import { NButtonGroup, NImage, NSpace, NButton, NDropdown } from 'naive-ui';
import { computed, ref } from 'vue';

const platform = navigator.platform.toLowerCase()
const plat = ref<"win" | 'linux' | 'mac'>('win')
if (platform.includes('win')) {
  plat.value = 'win'
} else if (platform.includes('linux')) {
  plat.value = 'linux'
} else if (platform.includes('mac')) {
  plat.value = 'mac'
}

console.log(plat.value)

const res = await fetch('https://api.nn.ci/proxy/https://github.com/alist-org/desktop-release/releases/latest/download/alist-desktop-proxy.json')
const data = await res.json()
const version = (data.name as string).slice(1)
const urls = {
  'win': `https://ghproxy.com/https://github.com/alist-org/desktop-release/releases/latest/download/alist-desktop_${version}_x64_en-US.msi`,
  'linux': `https://ghproxy.com/https://github.com/alist-org/desktop-release/releases/latest/download/alist-desktop_${version}_amd64.deb`,
  'mac': `https://ghproxy.com/https://github.com/alist-org/desktop-release/releases/latest/download/alist-desktop_${version}_x64.dmg`
}

let text = {
  down: "Download"
}

const raw = [
  { key: 'win', label: 'Windows' },
  { key: 'linux', label: 'Linux' },
  { key: 'mac', label: 'MacOS' }
]

const fullPlat = computed(() => {
  return raw.find(item => item.key === plat.value)?.label
})

const options = computed(() => {
  return raw.map(item => {
    return {
      ...item,
      label: `${item.label} ${plat.value === item.key ? '✅' : ''}`
    }
  })
})

if (location.pathname.startsWith("/zh/")) {
  text = {
    down: "下载"
  }
}

function down() {
  window.open(urls[plat.value], "_blank")
}

function handleSelect(key) {
  plat.value = key
}

</script>

<template>
  <NSpace align="center" vertical size="large">
    <NButtonGroup class="down">
      <NDropdown trigger="hover" :options="options" @select="handleSelect" size="large">
        <NButton size="large" type="primary" tertiary>💻{{ fullPlat }}</NButton>
      </NDropdown>
      <NButton size="large" @click="down" type="info" secondary>{{ text.down }}</NButton>
    </NButtonGroup>
    <NImage src="/img/guide/desktop.png"></NImage>
  </NSpace>
</template>

<style scoped>
.down {
  transform: scale(150%);
  margin-top: 30px;
  margin-bottom: 30px;
}
</style>