<script>
  function addGPSBox() {
    const scene = document.querySelector("a-scene");
    navigator.geolocation.getCurrentPosition(
      position => {
        const latitude = position.coords.latitude;
        const longitude = position.coords.longitude;
        //alert(latitude,longitude);
        const sphere = document.createElement("a-sphere");
        sphere.setAttribute(
          "gps-entity-place",
          `latitude:${latitude}; longitude:${longitude}`
        );
        sphere.setAttribute("radius", "3");
        sphere.setAttribute("scale", "2 2 2");
        sphere.setAttribute("color", randomHsl());
        sphere.addEventListener("loaded", () =>
          window.dispatchEvent(new CustomEvent("gps-entity-place-loaded"))
        );

        scene.appendChild(sphere);
      },
      err => console.log(err),
      {
        enableHighAccuracy: true,
        maximumAge: 0,
        timeout: 27000
      }
    );
  }

  const randomHsl = () => `hsla(${Math.random() * 360}, 100%, 50%, 1)`;

  function insertBox() {
    const scene = document.querySelector("a-scene");
    let sphere = document.createElement("a-sphere");
    sphere.setAttribute("ar-sphere", "true");
    sphere.object3D.position.set(0, 1.5, -4);
    sphere.setAttribute("radius", "2");
    sphere.setAttribute("color", randomHsl());
    scene.appendChild(sphere);
  }

  function clearBox() {
    const boxes = document.querySelectorAll("[ar-sphere]");
    boxes.forEach(box => {
      box.parentNode.removeChild(box);
    });
  }
</script>

<style>
  #add-ar {
    position: fixed;
    left: 50%;
    bottom: 15%;
    transform: translate(-50%, -50%);
    width: 90%;
    font-size: 1rem;
    z-index: 2;
    display: flex;
  }

  #add-ar button {
    flex: 1;
  }

  #add-gps-ar {
    position: fixed;
    left: 50%;
    bottom: 10%;
    transform: translate(-50%, -50%);
    width: 50%;
    font-size: 1rem;
    z-index: 2;
  }
</style>

<a-scene
  embedded
  gps-camera-debug
  arjs="sourceType: webcam; sourceWidth:1280; sourceHeight:960; displayWidth:
  1280; displayHeight: 960; debugUIEnabled: false;"
  cursor="rayOrigin: mouse; fuse: true; fuseTimeout: 0;"
  vr-mode-ui="enabled: false">
  <a-sphere
    radius="1.25"
    scale="2 2 2"
    gps-entity-place="latitude:-33.938278;longitude:151.199671" />
  <a-camera gps-camera="alert:true;" rotation-reader />
</a-scene>

<div id="add-ar">
  <button on:click={insertBox}>Add Box</button>
  <button on:click={clearBox}>Clear Box</button>
</div>

<button id="add-gps-ar" on:click={addGPSBox}>Add GPS Box</button>
