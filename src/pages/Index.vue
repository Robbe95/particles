<template>
  <button @click="swap">
    Swap
  </button>
  <div id="three" class="w-screen h-screen three overflow-hidden" />
</template>

<script setup>
import * as THREE from 'three'
import { onMounted } from 'vue'
import Circle from '@/assets/circle.png'
let camera, scene, renderer, stats, parameters
const mouseX = 0; const mouseY = 0

let windowHalfX = window.innerWidth / 2
let windowHalfY = window.innerHeight / 2

const materials = []

let active = true
const swap = () => {
  if (active) {
    for (let i = 0; i < materials.length; i++) {
      const color = parameters[i][0]
      materials[i].color.setHSL(0, 0.03, 0.5)
    }
  }
  else {
    for (let i = 0; i < materials.length; i++) {
      const color = parameters[i][0]
      materials[i].color.setHSL(0, 1, 0.5)
    }
  }
  active = !active
}

onMounted(() => {
  init()
  animate()
},
)
function init() {
  camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 10000)
  camera.position.z = 2000

  scene = new THREE.Scene()
  scene.background = 'transparent'

  // scene.fog = new THREE.FogExp2(0x000000, 0.0008)

  let geometry = new THREE.BufferGeometry()
  let vertices = []

  const textureLoader = new THREE.TextureLoader()
  const map = new THREE.TextureLoader().load('https://threejs.org/examples/textures/sprites/ball.png')
  const material = new THREE.SpriteMaterial({ map })

  const sprite = new THREE.Sprite(material)

  parameters = [
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],
    [[0, 1, 0.5], 1, 20],
    [[0, 1, 0.5], 1, 15],
    [[0, 1, 0.5], 1, 10],
    [[0, 1, 0.5], 1, 8],
    [[0, 1, 0.5], 1, 5],

  ]

  for (let i = 0; i < parameters.length; i++) {
    for (let i = 0; i < 100; i++) {
      const radius = 1300
      let x = THREE.Math.randFloat(-1, 1)
      let y = THREE.Math.randFloat(-1, 1)
      let z = THREE.Math.randFloat(-0.5, 0.5)
      const normalizationFactor = 0.5 / Math.sqrt(x * x + y * y)

      x = x * normalizationFactor * THREE.Math.randFloat(0.5 * radius, 1.2 * radius)
      y = y * normalizationFactor * THREE.Math.randFloat(0.5 * radius, 1.2 * radius)
      z = z * normalizationFactor * radius

      vertices.push(x, y, z)
    }

    geometry.setAttribute('position', new THREE.Float32BufferAttribute(vertices, 3))
    vertices = []
    geometry = new THREE.BufferGeometry()
    const color = parameters[i][0]
    // const sprite = parameters[i][1]
    const size = parameters[i][2]

    materials[i] = new THREE.PointsMaterial({
      size,
      blending: THREE.AdditiveBlending,
      depthTest: false,
      transparent: true,
      map: new THREE.TextureLoader().load('https://threejs.org/examples/textures/sprites/circle.png', (tex) => {
        tex.center.setScalar(0.5)
        tex.rotation = -Math.PI * 0.5
      }),
    })
    materials[i].color.setHSL(color[0], color[1], color[2])

    const particles = new THREE.Points(geometry, materials[i])
    particles.rotation.x = THREE.Math.randFloat(-1, 1) * 1000
    particles.rotation.y = THREE.Math.randFloat(-1, 1) * 1000
    particles.rotation.z = THREE.Math.randFloat(-1, 1) * 1000

    scene.add(particles)
  }

  //

  renderer = new THREE.WebGLRenderer({ alpha: true })
  renderer.setPixelRatio(window.devicePixelRatio)
  renderer.setSize(window.innerWidth, window.innerHeight)
  renderer.setClearColor(0xFFFFFF, 0)

  document.querySelector('#three').appendChild(renderer.domElement)

  //

  document.body.style.touchAction = 'none'
  document.body.addEventListener('pointermove', onPointerMove)

  //

  window.addEventListener('resize', onWindowResize)
}

function onWindowResize() {
  windowHalfX = window.innerWidth / 2
  windowHalfY = window.innerHeight / 2

  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()

  renderer.setSize(window.innerWidth, window.innerHeight)
}

function onPointerMove(event) {
  // if (event.isPrimary === false) return

  // mouseX = event.clientX - windowHalfX
  // mouseY = event.clientY - windowHalfY
}

//

function animate() {
  requestAnimationFrame(animate)

  render()
}
let counter = THREE.Math.randFloat(0, 100)
function render() {
  if (active)
    counter += 0.0001
  else
    counter += 0.00001
  // camera.position.x += (mouseX - camera.position.x) * 0.05
  // camera.position.y += (-mouseY - camera.position.y) * 0.05
  camera.lookAt(scene.position)
  for (let i = 0; i < scene.children.length; i++) {
    const object = scene.children[i]
    if (object instanceof THREE.Points) {
      object.rotation.y = counter * (i < 4 ? i + 1 : -(i + 1))
      object.rotation.x = counter * (i < 4 ? i + 1 : -(i + 1))
      object.rotation.z = counter * (i < 4 ? i + 1 : -(i + 1))
    }
  }

  for (let i = 0; i < materials.length; i++) {
    const color = parameters[i][0]

    // const h = (360 * (color[0] + time) % 360) / 360
    // materials[i].color.setHSL(h, color[1], color[2])
  }

  renderer.render(scene, camera)
}
</script>
