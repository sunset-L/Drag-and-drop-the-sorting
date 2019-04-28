<template>
  <div class="home">
    <ul class="list animate" ref="option">
      <li 
        class="listItem" 
        v-for="(li,idx) in list" 
        :key="li" 
      >
        {{li}}
        <span class="drag" @mousedown="mousedown(li, idx, $event)"></span>
      </li>
    </ul>
  </div>
</template>

<script>

export default {
  name: 'home',
  data() {
    return {
      list: [1, 2, 3, 4, 5],
      event: 'mousemove'
    }
  },
  methods: {
    mousedown(item, index, e) {
      let $elem = e.target.parentNode
      let $brothers = Array.prototype.slice.call(this.$refs.option.children, 0)
      let lastY = e.pageY
      let range = [-(index * 40), ($brothers.length -1 - index) * 40]
      let target = index
      $elem.classList.add('scale')
      const mousemove = (ev) => {
        let moveY = ev.pageY - lastY
        if (moveY < range[0]) moveY = range[0]
        if (moveY > range[1]) moveY = range[1]
        let tmp = index + Math.round(moveY / 40)

        if (tmp !== target) {
          $brothers.forEach((it, i) => {
            if (i === index) return
            if (moveY > 0) {
              // 往下走
              if (i < index) return
              if (i <= tmp) {
                it.style.transform = `translateY(-40px)`
              } else {
                it.style.transform = ''
              }
            } else {
              // 往上走
              if (i > index) return
              if (i < tmp) {
                it.style.transform = ''
              } else {
                it.style.transform = `translateY(40px)`
              }
            }
          })
          target = tmp
        }
        
        $elem.style.transform = `translateY(${moveY}px) scale(1.05)`
      }
      const mouseup = (e) => {
        $elem.classList.remove('scale')
        $elem.style.transform = `translateY(${40 * (target - index)}px)`

        if (target !== index) {
          // 临时关闭所有动画
          this.$refs.option.classList.remove('animate')

          let tmp = this.list.splice(index, 1)[0]
          this.list.splice(target, 0, tmp)

          // 位移重置
          $brothers.forEach(it => {
            it.style.transform = ''
          })
          this.$nextTick(() => {
            this.$refs.option.classList.add('animate')
          })
        }

        document.removeEventListener('mousemove', mousemove)
        document.removeEventListener('mouseup', mouseup)
      }
      document.addEventListener('mousemove', mousemove)
      document.addEventListener('mouseup', mouseup)
    },
  }
}
</script>
<style>
  .scale{
    transform: scale(1.05);
    box-shadow: 0 0 8px rgba(0, 0, 0, 0.15);
  }
  .home{
    width: 400px;
    margin: 0 auto;
  }
  .list{
    list-style: none;
    padding: 0;
    width: 100%;
  }
  .listItem{
    border: 1px solid pink;
    height: 40px;
    line-height: 40px;
    width: 100%;
    position: relative;
    user-select: none;
  }
  .animate .listItem{
    transition: transform 0.1s linear;
  }
  .listItem:nth-of-type(2n){
    border-top: none;
    border-bottom: none;
  }
  .drag{
    border: 1px solid pink;
    width: 20px;
    height: 20px;
    display: inline-block;
    position: absolute;
    right: 10px;
    top: 10px;
    cursor: pointer;
  }
</style>
