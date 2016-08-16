# How to convert your three.js app to web VR on VIVE

#### Add mesh and texture files for VIVE controllers
models/obj/vive-controller/vr_controller_vive_1_5.obj  
models/obj/vive-controller/onepointfive_texture.png  
models/obj/vive-controller/onepointfive_spec.png  

#### Right at the beginning, before anything
```javascript
if (WEBVR.isLatestAvailable() === false) document.body.appendChild(WEBVR.getMessage());
```

#### Add global variables
```javascript
var effect, controls, controller1, controller2;
```

#### Camera near and far are changed
```javascript
camera = new THREE.PerspectiveCamera(70, window.innerWidth / window.innerHeight, 0.001, 500);
// remove camera.position
```

#### Use VIVE's movement and controllers
```javascript
// instead of OrbitControls
controls = new THREE.VRControls(camera);
controls.standing = true;
controller1 = new THREE.ViveController(0);
controller1.standingMatrix = controls.getStandingMatrix();
scene.add(controller1);

controller2 = new THREE.ViveController(1);
controller2.standingMatrix = controls.getStandingMatrix();
scene.add(controller2);

var loader = new THREE.OBJLoader();
loader.setPath('models/obj/vive-controller/');
loader.load('vr_controller_vive_1_5.obj', function (object) {
    var loader = new THREE.TextureLoader();
    loader.setPath('models/obj/vive-controller/');
    var controller = object.children[0];
    controller.material.map = loader.load('onepointfive_texture.png');
    controller.material.specularMap = loader.load('onepointfive_spec.png');
    controller1.add(object.clone());
    controller2.add(object.clone());
});

effect = new THREE.VREffect(renderer);
if (WEBVR.isAvailable() === true) {
    document.body.appendChild(WEBVR.getButton(effect));
}
```

#### Change on windowResize()
```javascript
effect.setSize(window.innerWidth, window.innerHeight);
```

#### Change on animate() 
```javascript
effect.requestAnimationFrame(animate);
```

#### Change on render()
```javascript
effect.render(scene, camera);
```

