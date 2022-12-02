<template>
	<view class="content" id="container">
		<canvas id="canvas_webgl" type="webgl" disable-scroll="true" @touchcancel="webgl_touch"
			@touchend="webgl_touch" @touchmove="webgl_touch" @touchstart="webgl_touch" />
	</view>
</template>

<script>
	import {
		document,
		window,
		self,
		URL,
		requestAnimationFrame,
		cancelAnimationFrame,
		Event
	} from 'dhtml-weixin'
	const THREE = requirePlugin('ThreeX');
	import {
		GLTFLoader
	} from 'three/jsm/loaders/GLTFLoader.js';
	import {
		DRACOLoader
	} from 'three/jsm/loaders/DRACOLoader.js'
	import Stats from 'three/jsm/libs/stats.module.js';
	import {
		GUI
	} from 'three/jsm/libs/lil-gui.module.min.js';
	import {
		OrbitControls
	} from 'three/jsm/controls/OrbitControls.js';
	export default {
		data() {
			return {
				title: 'Hello',
				scene: null,
				camera: null,
				renderer: null,
				controls: null,
				cube: null,
				canvas3d: null,
				AnimationFrame: null,
			}
		},
		onLoad() {
			this.init();
		},
		onUnload() {
			cancelAnimationFrame(this.AnimationFrame, this.renderer.domElement);
		},
		methods: {
			webgl_touch(e) {
				const web_e = Event.fix(e)
				document.dispatchEvent(web_e)
				window.dispatchEvent(web_e)
				this.renderer && this.renderer.domElement.dispatchEvent(web_e);
			},
			animate() {
				this.AnimationFrame = requestAnimationFrame(this.animate, this.renderer.domElement);
				this.controls.update();
				// this.cube.rotation.x += 0.02;
				// this.cube.rotation.y += 0.02;
				this.renderer.render(this.scene, this.camera);
			},
			onWindowResize() {
				this.camera.aspect = window.innerWidth / window.innerHeight;
				this.camera.updateProjectionMatrix();
				this.renderer.setSize(window.innerWidth, window.innerHeight);
			},

			async init() {
				const container = document.getElementById('container');
				this.canvas3d = this.canvas = await document.createElementAsync("canvas", "webgl")

				this.scene = new THREE.Scene();
				this.scene.background = new THREE.Color(0xe0e0e0);
				this.scene.fog = new THREE.Fog(0xa0a0a0, 10, 50);

				// const hemiLight = new THREE.HemisphereLight(0xffffff, 0x444444);
				// hemiLight.position.set(0, 20, 0);
				// this.scene.add(hemiLight);

				// const dirLight = new THREE.DirectionalLight(0xffffff);
				// dirLight.position.set(3, 10, 10);
				// dirLight.castShadow = true;
				// dirLight.shadow.camera.top = 2;
				// dirLight.shadow.camera.bottom = -2;
				// dirLight.shadow.camera.left = -2;
				// dirLight.shadow.camera.right = 2;
				// dirLight.shadow.camera.near = 0.1;
				// dirLight.shadow.camera.far = 40;
				// this.scene.add(dirLight);


				// const mesh = new THREE.Mesh(new THREE.PlaneGeometry(10, 10), new THREE.MeshPhongMaterial({
				// 	color: 0xeeeeee,
				// 	depthWrite: false
				// }));
				// mesh.rotation.x = -Math.PI / 2;
				// mesh.receiveShadow = true;
				// this.scene.add(mesh);
				// camera
				this.camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 1, 100);
				this.camera.position.set(0, -2, 1);

				this.renderer = new THREE.WebGLRenderer({
					canvas: this.canvas3d,
					antialias: true
				});
				this.renderer.setPixelRatio(window.devicePixelRatio);
				this.renderer.setSize(window.innerWidth, window.innerHeight);
				this.renderer.outputEncoding = THREE.sRGBEncoding;
				this.renderer.shadowMap.enabled = true;
				container.appendChild(this.renderer.domElement);

				//创建一个正方体
				const geometry = new THREE.BoxGeometry(1, 1, 1);
				const material = new THREE.MeshBasicMaterial({
					color: 0x00ff00
				});
				this.cube = new THREE.Mesh(geometry, material);
				this.scene.add(this.cube);

				this.controls = new OrbitControls(this.camera, this.renderer.domElement);
				// this.controls = new OrbitControls(this.camera, this.canvas3d);
				this.controls.target.set(0, 0, -1);
				this.controls.enableDamping = true; // 使动画循环使用时阻尼或自转 意思是否有惯性 
				this.controls.enableZoom = true; //是否可以缩放 
				this.controls.autoRotate = false; //是否自动旋转 
				// this.controls.minDistance = 200; //设置相机距离原点的最远距离 
				// this.controls.maxDistance = 600; //设置相机距离原点的最远距离 
				this.controls.enablePan = true; //是否开启右键拖拽 
				this.controls.addEventListener('change', (e) => {
					console.log("改变了", e);
				})
				this.controls.update();
				// this.renderer.render(this.scene, this.camera);
				// return;
				const loader = new GLTFLoader();
				const that = this;
				window.addEventListener('resize', this.onWindowResize);
			
					that.animate();
				
			}
		}
	}
</script>

<style scoped lang="scss">
	.content {
		height: 100vh;
		width: 100%;

		#canvas_webgl {
			height: 100%;
			width: 100%;
		}
	}
</style>
