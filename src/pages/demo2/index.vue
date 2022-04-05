<script setup lang="ts">
import { onMounted } from 'vue'
import Phaser from 'phaser'

interface XY {
  x: number
  y: number
}
interface PipeInfo {
  top: XY
  bottom: XY
}

let player: Phaser.Physics.Arcade.Sprite
let pipe: Phaser.Physics.Arcade.Group
let cursors: Phaser.Types.Input.Keyboard.CursorKeys
let scoreText: Phaser.GameObjects.Text
let score = 0
let gameOver = false

onMounted(() => {
  const config: Phaser.Types.Core.GameConfig = {
    type: Phaser.AUTO,
    width: 288,
    height: 512,
    parent: 'game',
    physics: {
      default: 'arcade',
      arcade: {
        gravity: { x: -20 },
        debug: false,
      },
    },
    scene: {
      preload,
      create,
      update,
    },
  }
  // 初始化Game
  // eslint-disable-next-line no-new
  new Phaser.Game(config)
})

// 资源预加载
function preload(this: Phaser.Scene) {
  this.load.image('sky', 'assets/background.png')
  this.load.image('pipetop', 'assets/pipetop.png')
  this.load.image('pipebottom', 'assets/pipebottom.png')
  this.load.spritesheet('bird', 'assets/birds.png', { frameWidth: 30, frameHeight: 30 })
}

function create(this: Phaser.Scene) {
  // 设置背景居中
  this.add.image(144, 256, 'sky')
  // 创建管道组对象
  pipe = this.physics.add.group()
  // 管道数组
  let pipeArr: PipeInfo[] = []
  if (!pipeArr.length)
    pipeArr = generationPipe()

  pipeArr.forEach((item) => {
    pipe.create(item.top.x, item.top.y, 'pipetop')
    pipe.create(item.bottom.x, item.bottom.y, 'pipebottom')
  })

  // 设置角色
  player = this.physics.add.sprite(80, 250, 'bird')

  player.setBounce(0.2)
  player.setCollideWorldBounds(true)

  // 创建动画
  this.anims.create({
    key: 'fly',
    frames: this.anims.generateFrameNumbers('bird', { start: 0, end: 2 }),
    frameRate: 10,
    repeat: -1,
  })

  this.anims.create({
    key: 'stop',
    frames: [{ key: 'bird', frame: 4 }],
    frameRate: 20,
  })

  //  键盘事件
  cursors = this.input.keyboard.createCursorKeys()

  //  成绩文本
  scoreText = this.add.text(16, 16, 'score: 0', { fontSize: '32px', fill: '#000' } as Phaser.Types.GameObjects.Text.TextStyle)

  //  添加碰撞检测
  this.physics.add.collider(player, pipe, hitPipe as unknown as ArcadePhysicsCallback, undefined, this)

  // 使用requestAnimationFrame构建定时器
  let ts = +(new Date())
  const scoreLoop = () => {
    if (gameOver)
      return
    const nts = +(new Date())
    if (nts - ts > 1000) {
      ts = nts
      score += 1
      scoreText.setText(`score: ${score}`)
    }
    window.requestAnimationFrame(scoreLoop)
  }

  // 开始计时
  scoreLoop()
}

function update() {
  if (gameOver)
    return

  if (cursors.space.isDown) {
    player.setVelocityY(-300)

    player.anims.play('fly', true)
  }

  else {
    player.setVelocityX(0)
    player.setVelocityY(50)
  }
}

function hitPipe(this: Phaser.Scene, player: Phaser.Physics.Arcade.Sprite, pipe: Phaser.Physics.Arcade.Group) {
  this.physics.pause()
  player.anims.play('stop')
  gameOver = true
}

function generationPipe() {
  const result = []
  for (let i = 0; i < 222; i++) {
    // 间隔系数
    const step = i * 5
    // 上下管道间隔
    const space = 200 - (step > 140 ? 140 : step)
    // 上管道
    const topOffset = Phaser.Math.Between(0 - step - 100, 0)
    // 下管道
    const bottomOffset = 512 + topOffset + space
    result.push({
      top: {
        x: 250 + (i * 150),
        y: topOffset,
      },
      bottom: {
        x: 250 + (i * 150),
        y: bottomOffset,
      },
    })
  }
  return result
}
</script>

<template>
  <div>
    <div id="game" inline-block />
  </div>
</template>
