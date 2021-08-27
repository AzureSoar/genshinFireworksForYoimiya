<template>
<div>
	<div id="container"></div>
	<!-- <h1>{{ msg }}</h1>
	<button @click="count++">count is: {{ count }}</button> -->
	<!-- SVG Spritesheet -->

</div>
</template>

<script>
import * as THREE from "three";
import {OrbitControls} from 'three/examples/jsm/controls/OrbitControls.js';
let scene = null;
let mesh = null;
let group = null;
let delta = null;
let clock = new THREE.Clock();
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      colorMap:['#39c5bb','#FFA500','#FFE211','#FFC0CB','#D80000','#0000FF'],
      //         MIKU      RIN       LEN       LUKA      MEIKO     KAITO
      count: 0,
      // scene: null,
      // mesh: null,
	    camera: null,
      renderer: null,
      controls:null
    }
  },
  mounted() {
    this.init();
    this.animate();
  },
  methods: {
    //初始化
    init: function() {
      let container = document.getElementById("container");

      // 初始化场景
      scene = new THREE.Scene();

      // 初始化相机
      //透视相机
      this.camera = new THREE.PerspectiveCamera(
        75,
        // window.innerWidth/window.innerHeight,
        container.clientWidth / container.clientHeight,
        1,
        1000
      );
      //正交相机
      // this.camera = new THREE.OrthographicCamera( 
      //  container.clientWidth / - 2,
      //  container.clientWidth / 2, 
      //  container.clientHeight / 2, 
      //  container.clientHeight / - 2, 
      //  1, 
      //  1000 );
      // this.camera.position.y = 500;
      // this.camera.position.z = 100;
      this.camera.position.set(0,0,300);

      // // 创建渲染器对象
      this.renderer = new THREE.WebGLRenderer({
        antialias: true,
      });
      // this.renderer = new THREE.CanvasRenderer({
      //   antialias: true,
      // });
      // this.renderer.setSize(window.innerWidth,window.innerHeight);
      this.renderer.setSize(container.clientWidth, container.clientHeight);
      this.renderer.setClearColor(0xffffff,0.5)
      container.appendChild(this.renderer.domElement);
      // document.body.appendChild(this.renderer.domElement);

      // // 添加坐标系
      // let axes = new THREE.AxisHelper(500);
      // scene.add(axes);

      // 网格模型添加到场景中
      //立方体
      // let geometry = new THREE.BoxBufferGeometry(1,1,1);
      // let material = new THREE.MeshBasicMaterial({
      //   color:'#39C5BB'
      // });
      // mesh = new THREE.Mesh(geometry,material);
      // scene.add(mesh);

      //边框
      // let box = new THREE.BoxHelper(mesh,0xffff00);
      // scene.add(box);

      //线
      // let lineMaterial = new THREE.LineBasicMaterial({color:0x0000ff})
      // let lineGeometry = new THREE.BufferGeometry();
      // lineGeometry.vertices.push(new THREE.Vector3(-10,0,0));
      // lineGeometry.vertices.push(new THREE.Vector3(0,10,0));
      // lineGeometry.vertices.push(new THREE.Vector3(10,0,0));
      // let line = new THREE.Line(lineGeometry,lineMaterial);
      // scene.add(line);


      group = new THREE.Group();
      for (let i = 0; i < 20; i++) {
        //球 点
        let geometry = new THREE.SphereBufferGeometry(1,32,32);
        let material = new THREE.MeshBasicMaterial({
          // color:this.colorMap[this.colorMap.length*Math.random()|0]
          color:'#FFA500'
        });
        mesh = new THREE.Mesh(geometry,material);
        mesh.position.y = -100;
        mesh.position.x = this.ndr()*100
        group.add(mesh);
      }
      scene.add(group);

      //sprite
      //画点
      // let PI2 = Math.PI*2;
      // let program = function (context) {
      //   context.beginPath();
      //   context.arc(0,0,0.5,0,PI2,true);
      //   context.fill();
      // }
      // //点材质
      // let textureLoader = new THREE.TextureLoader();
      // let sprite = textureLoader.load('../assets/snowflake.png');
      // let material = new THREE.SpriteCanvasMaterial({
      //   color: Math.random()*0x808008+0x808008,
      //   program:program
      // });
      // mesh = new THREE.Sprite(material);
      // mesh.position.x = 0;
      // mesh.position.y = 0;
      // mesh.position.z = 0;
      // scene.add(mesh);

      //创建控件对象
      this.controls = new OrbitControls(this.camera, this.renderer.domElement);
      // console.log(this.controls.mouseButtons);
      // this.controls.autoRotate = true;
      this.controls.addEventListener('click',this.handleClick);
    },

    // 动画
    animate: function() {
      requestAnimationFrame(this.animate);
      group.traverse(this.launch)
      // this.launch();
      // mesh.rotation.x += 0.01;
      // mesh.rotation.y += 0.02;
      // this.controls.update();
      this.renderer.render(scene, this.camera);
    },
    // 发射
    launch: function (comet) {
      // console.log(comet);
      // delta = clock.getDelta();
      if(comet.position.y<100) {
        let magicNum = this.ndr();
        comet.position.y += 1*magicNum;
        if(comet['material']){
          // console.log(comet.material.opacity);
          comet.material.opacity *=magicNum;
        }
      }else {
        // console.log(delta);
      }
      comet.position.x = this.fsin(comet.position.y);
    },
    // 点击事件
    handleClick: function (rest) {
      console.log(rest);
    },
    // 正弦函数
    fsin: function (x) {
      return 2.5*Math.sin(20*x*Math.PI/180);
    },
    // 正态随机
    ndr: function(){
        var u=0.0, v=0.0, w=0.0, c=0.0;
        do{
            //获得两个（-1,1）的独立随机变量
            u=Math.random()*2-1.0;
            v=Math.random()*2-1.0;
            w=u*u+v*v;
        }while(w==0.0||w>=1.0)
        //这里就是 Box-Muller转换
        c=Math.sqrt((-2*Math.log(w))/w);
        //返回2个标准正态分布的随机数，封装进一个数组返回
        //当然，因为这个函数运行较快，也可以扔掉一个
        //return [u*c,v*c];
        return 1 - Math.abs(u*c)/2;
    }
    
  }
}

</script>
<style lang="stylus" scoped>
#container {
  position: absolute;
  width: 100%;
  height: 100%;
}
</style>