<template>
  <canvas ref="canvas" width="512" height="512" class="absolute top-0 left-0 w-full -z-10"></canvas>
</template>

<script>
import * as THREE from 'three';
import { KawaseBlurPass, EffectComposer, EffectPass, RenderPass } from 'postprocessing';

export default {
  data() {
    return {
      composer: null,
      renderer: null,
      scene: null,
      camera: null,
    }
  },
  mounted() {
    this.init();
    window.addEventListener('resize', this.handleResize);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.handleResize);
  },
  methods: {
    init() {
      this.scene = new THREE.Scene();
      this.camera = new THREE.PerspectiveCamera( 75, window.innerWidth / window.innerHeight, 0.1, 1000 );

      this.renderer = new THREE.WebGLRenderer({
        canvas: this.$refs.canvas,
        // antialias: true,
        powerPreference: "high-performance",
        antialias: false,
        stencil: false,
        depth: false
      });

      this.composer = new EffectComposer(this.renderer);
      this.composer.addPass(new RenderPass(this.scene, this.camera));
      this.composer.addPass(new KawaseBlurPass({
        height: 480
      }));

      this.renderer.setSize( window.innerWidth, window.innerHeight );

      // const geometry = new THREE.BoxGeometry();
      // const material = new THREE.MeshBasicMaterial( { color: 0x00ff00 } );
      // const cube = new THREE.Mesh( geometry, material );
      // scene.add( cube );

      const geometry = new THREE.CircleGeometry( 2, 16 );
      const material = new THREE.MeshBasicMaterial( { color: 0xffff00 } );
      const circle = new THREE.Mesh( geometry, material );
      this.scene.add( circle );


      this.camera.position.z = 5;

      this.animate();
    },
    animate() {
      requestAnimationFrame(this.animate);
      this.composer.render();
    },

    handleResize() {
      this.camera.aspect = window.innerWidth / window.innerHeight;
      this.camera.updateProjectionMatrix();
      this.renderer.setSize(window.innerWidth, window.innerHeight);
    }
  }
}
</script>