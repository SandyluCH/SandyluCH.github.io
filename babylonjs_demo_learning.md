小汽车（轮子滚动）
````
<!DOCTYPE html>
<html>

<head>
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />

    <title>Babylon.js sample code</title>

    <!-- Babylon.js -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/dat-gui/0.6.2/dat.gui.min.js"></script>
    <script src="https://assets.babylonjs.com/generated/Assets.js"></script>
    <script src="https://preview.babylonjs.com/ammo.js"></script>
    <script src="https://preview.babylonjs.com/cannon.js"></script>
    <script src="https://preview.babylonjs.com/Oimo.js"></script>
    <script src="https://preview.babylonjs.com/earcut.min.js"></script>
    <script src="https://preview.babylonjs.com/babylon.js"></script>
    <script src="https://preview.babylonjs.com/materialsLibrary/babylonjs.materials.min.js"></script>
    <script src="https://preview.babylonjs.com/proceduralTexturesLibrary/babylonjs.proceduralTextures.min.js"></script>
    <script src="https://preview.babylonjs.com/postProcessesLibrary/babylonjs.postProcess.min.js"></script>
    <script src="https://preview.babylonjs.com/loaders/babylonjs.loaders.js"></script>
    <script src="https://preview.babylonjs.com/serializers/babylonjs.serializers.min.js"></script>
    <script src="https://preview.babylonjs.com/gui/babylon.gui.min.js"></script>
    <script src="https://preview.babylonjs.com/inspector/babylon.inspector.bundle.js"></script>

    <style>
        html,
        body {
            overflow: hidden;
            width: 100%;
            height: 100%;
            margin: 0;
            padding: 0;
        }

        #renderCanvas {
            width: 100%;
            height: 100%;
            touch-action: none;
        }
    </style>
</head>

<body>
    <canvas id="renderCanvas"></canvas>
    <script>
        var canvas = document.getElementById("renderCanvas");

        var startRenderLoop = function (engine, canvas) {
            engine.runRenderLoop(function () {
                if (sceneToRender && sceneToRender.activeCamera) {
                    sceneToRender.render();
                }
            });
        }

        var engine = null;
        var scene = null;
        var sceneToRender = null;
        var createDefaultEngine = function () { return new BABYLON.Engine(canvas, true, { preserveDrawingBuffer: true, stencil: true, disableWebGL2Support: false }); };
        const createScene = () => {
            const scene = new BABYLON.Scene(engine);

            const camera = new BABYLON.ArcRotateCamera("camera", -Math.PI / 2, Math.PI / 2.5, 3, new BABYLON.Vector3(0, 0, 0));
            camera.attachControl(canvas, true);
            const light = new BABYLON.HemisphericLight("light", new BABYLON.Vector3(0, 1, 0));

            const car = buildCar(scene);
            car.rotation.x = -Math.PI / 2;

            return scene;
        }

        const buildCar = (scene) => {

            //base
            const outline = [
                new BABYLON.Vector3(-0.3, 0, -0.1),
                new BABYLON.Vector3(0.2, 0, -0.1),
            ]

            //curved front
            for (let i = 0; i < 20; i++) {
                outline.push(new BABYLON.Vector3(0.2 * Math.cos(i * Math.PI / 40), 0, 0.2 * Math.sin(i * Math.PI / 40) - 0.1));
            }

            //top
            outline.push(new BABYLON.Vector3(0, 0, 0.1));
            outline.push(new BABYLON.Vector3(-0.3, 0, 0.1));

            //back formed automatically

            //car face UVs
            const faceUV = [];
            faceUV[0] = new BABYLON.Vector4(0, 0.5, 0.38, 1);
            faceUV[1] = new BABYLON.Vector4(0, 0, 1, 0.5);
            faceUV[2] = new BABYLON.Vector4(0.38, 1, 0, 0.5);

            //car material
            const carMat = new BABYLON.StandardMaterial("carMat");
            carMat.diffuseTexture = new BABYLON.Texture("https://assets.babylonjs.com/environments/car.png");

            const car = BABYLON.MeshBuilder.ExtrudePolygon("car", { shape: outline, depth: 0.2, faceUV: faceUV, wrap: true });
            car.material = carMat;

            //wheel face UVs
            const wheelUV = [];
            wheelUV[0] = new BABYLON.Vector4(0, 0, 1, 1);
            wheelUV[1] = new BABYLON.Vector4(0, 0.5, 0, 0.5);
            wheelUV[2] = new BABYLON.Vector4(0, 0, 1, 1);

            //car material
            const wheelMat = new BABYLON.StandardMaterial("wheelMat");
            wheelMat.diffuseTexture = new BABYLON.Texture("https://assets.babylonjs.com/environments/wheel.png");

            const wheelRB = BABYLON.MeshBuilder.CreateCylinder("wheelRB", { diameter: 0.125, height: 0.05, faceUV: wheelUV })
            wheelRB.material = wheelMat;
            wheelRB.parent = car;
            wheelRB.position.z = -0.1;
            wheelRB.position.x = -0.2;
            wheelRB.position.y = 0.035;

            wheelRF = wheelRB.clone("wheelRF");
            wheelRF.position.x = 0.1;

            wheelLB = wheelRB.clone("wheelLB");
            wheelLB.position.y = -0.2 - 0.035;

            wheelLF = wheelRF.clone("wheelLF");
            wheelLF.position.y = -0.2 - 0.035;


            //Animate the Wheels
            const animWheel = new BABYLON.Animation("wheelAnimation", "rotation.y", 30, BABYLON.Animation.ANIMATIONTYPE_FLOAT, BABYLON.Animation.ANIMATIONLOOPMODE_CYCLE);

            const wheelKeys = [];

            //At the animation key 0, the value of rotation.y is 0
            wheelKeys.push({
                frame: 0,
                value: 0
            });

            //At the animation key 30, (after 1 sec since animation fps = 30) the value of rotation.y is 2PI for a complete rotation
            wheelKeys.push({
                frame: 30,
                value: 2 * Math.PI
            });

            //set the keys
            animWheel.setKeys(wheelKeys);

            //Link this animation to a wheel
            wheelRB.animations = [];
            wheelRB.animations.push(animWheel);

            //Link this animation to a wheel
            wheelRF.animations = [];
            wheelRF.animations.push(animWheel);

            // //Link this animation to a wheel
            wheelLB.animations = [];
            wheelLB.animations.push(animWheel);

            // //Link this animation to a wheel
            wheelLF.animations = [];
            wheelLF.animations.push(animWheel);

            scene.beginAnimation(wheelRB, 0, 30, true);
            scene.beginAnimation(wheelRF, 0, 30, true);
            scene.beginAnimation(wheelLB, 0, 30, true);
            scene.beginAnimation(wheelLF, 0, 30, true);



            return car;
        }
        window.initFunction = async function () {


            var asyncEngineCreation = async function () {
                try {
                    return createDefaultEngine();
                } catch (e) {
                    console.log("the available createEngine function failed. Creating the default engine instead");
                    return createDefaultEngine();
                }
            }

            window.engine = await asyncEngineCreation();
            if (!engine) throw 'engine should not be null.';
            startRenderLoop(engine, canvas);
            window.scene = createScene();
        };
        initFunction().then(() => {
            sceneToRender = scene
        });

        // Resize
        window.addEventListener("resize", function () {
            engine.resize();
        });
    </script>
</body>

</html>

````
