<script setup lang="ts">
const name = $ref('demo2')

const router = useRouter()
const go = () => {
  if (name)
    router.push(`/${encodeURIComponent(name)}`)
}
</script>

<template>
  <div>
    <div i-carbon-campsite text-4xl inline-block />
    <p>
      <a rel="noreferrer" href="https://github.com/antfu/vitesse-lite" target="_blank">
        xc phaser demo
      </a>
    </p>
    <p>
      <em text-sm op75>游戏列表</em>
    </p>
    <p>
      <em text-sm op75>demo1--官方demo</em>
    </p>
    <p>
      <em text-sm op75 color="red">demo2--FlappyBird--self(像素鸟)</em>
    </p>

    <div py-4 />

    <!-- <input
      id="input"
      v-model="name"
      placeholder="What's your name?"
      type="text"
      autocomplete="false"
      p="x-4 y-2"
      w="250px"
      text="center"
      bg="transparent"
      border="~ rounded gray-200 dark:gray-700"
      outline="none active:none"
      @keydown.enter="go"
    > -->
    <select
      v-model="name"
      placeholder="选择一个游戏"
      p="x-4 y-2"
      w="250px"
      text="center"
      bg="transparent"
      border="~ rounded gray-200 dark:gray-700"
      outline="none active:none"
      @keydown.enter="go"
    >
      <option value="demo1" p-10px>
        demo1
      </option>
      <option value="demo2">
        demo2
      </option>
    </select>

    <div>
      <button
        class="m-3 text-sm btn"
        :disabled="!name"
        @click="go"
      >
        Go
      </button>
    </div>
  </div>
</template>
