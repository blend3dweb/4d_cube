<template>
    <div class="cube-container">
      <canvas ref="canvas" width="1000" height="800"></canvas>
      <div class="controls">
        <label>XY Rotation: <input type="range" v-model="rotationXY" min="0" max="360"></label>
        <label>XZ Rotation: <input type="range" v-model="rotationXZ" min="0" max="360"></label>
        <label>XW Rotation: <input type="range" v-model="rotationXW" min="0" max="360"></label>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'FourDCube',
    data() {
      return {
        ctx: null,
        points: [],
        rotationXY: 0,
        rotationXZ: 0,
        rotationXW: 0
      }
    },
    mounted() {
      this.ctx = this.$refs.canvas.getContext('2d')
      this.initCube()
      this.animate()
    },
    methods: {
      initCube() {
        // Generate 16 vertices of 4D cube
        for (let x = -1; x <= 1; x += 2) {
          for (let y = -1; y <= 1; y += 2) {
            for (let z = -1; z <= 1; z += 2) {
              for (let w = -1; w <= 1; w += 2) {
                this.points.push([x, y, z, w])
              }
            }
          }
        }
      },
      project4Dto3D(point, rotXY, rotXZ, rotXW) {
        let [x, y, z, w] = point
        
        // Apply rotations
        const cosXY = Math.cos(rotXY * Math.PI / 180)
        const sinXY = Math.sin(rotXY * Math.PI / 180)
        const cosXZ = Math.cos(rotXZ * Math.PI / 180)
        const sinXZ = Math.sin(rotXZ * Math.PI / 180)
        const cosXW = Math.cos(rotXW * Math.PI / 180)
        const sinXW = Math.sin(rotXW * Math.PI / 180)
  
        // Rotate in XY plane
        let temp = x
        x = x * cosXY - y * sinXY
        y = temp * sinXY + y * cosXY
  
        // Rotate in XZ plane
        temp = x
        x = x * cosXZ - z * sinXZ
        z = temp * sinXZ + z * cosXZ
  
        // Rotate in XW plane
        temp = x
        x = x * cosXW - w * sinXW
        w = temp * sinXW + w * cosXW
  
        // Project 4D to 3D (perspective projection)
        const distance = 2
        const w4 = 1 / (distance - w)
        return [x * w4, y * w4, z * w4]
      },
      project3Dto2D(point) {
        const [x, y] = point
        const scale = 200
        return [
          x * scale + this.$refs.canvas.width / 2,
          y * scale + this.$refs.canvas.height / 2
        ]
      },
      drawCube() {
        this.ctx.clearRect(0, 0, this.$refs.canvas.width, this.$refs.canvas.height)
        this.ctx.strokeStyle = '#00ff00'
        this.ctx.lineWidth = 2
  
        // Project and draw all points
        const projectedPoints = this.points.map(p => 
          this.project3Dto2D(
            this.project4Dto3D(p, this.rotationXY, this.rotationXZ, this.rotationXW)
          )
        )
  
        // Draw edges
        for (let i = 0; i < 16; i++) {
          for (let j = i + 1; j < 16; j++) {
            // Connect points that differ in only one coordinate
            const diff = this.points[i].reduce((acc, val, idx) => 
              acc + (val !== this.points[j][idx] ? 1 : 0), 0
            )
            if (diff === 1) {
              this.ctx.beginPath()
              this.ctx.moveTo(projectedPoints[i][0], projectedPoints[i][1])
              this.ctx.lineTo(projectedPoints[j][0], projectedPoints[j][1])
              this.ctx.stroke()
            }
          }
        }
      },
      animate() {
        this.rotationXY = (this.rotationXY + 0.5) % 360
        this.rotationXZ = (this.rotationXZ + 0.3) % 360
        this.rotationXW = (this.rotationXW + 0.2) % 360
        this.drawCube()
        requestAnimationFrame(this.animate)
      }
    }
  }  </script>  
  <style scoped>
  .cube-container {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .controls {
    margin-top: 20px;
  }
  canvas {
    border: 1px solid #333;
    background: #000;
  }
  </style>
  