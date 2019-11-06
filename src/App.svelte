<script>
  function addGPSBox() {
    const scene = document.querySelector("a-scene");
    navigator.geolocation.getCurrentPosition(
      position => {
        const latitude = position.coords.latitude;
        const longitude = position.coords.longitude;

        const sphere = document.createElement("a-sphere");
        sphere.setAttribute(
          "gps-entity-place",
          `latitude:${latitude}; longitude:${longitude}`
        );
        sphere.setAttribute("radius", "2");
        sphere.setAttribute("scale", "1 1 1");
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
    const boxes = document.querySelectorAll("[gps-entity-place]");
    boxes.forEach(box => {
      box.parentNode.removeChild(box);
    });
  }

  function offsetGPS() {
    let alpha = null;
    let initialOffset = null;
    if (window.DeviceOrientationEvent) {
      window.addEventListener(
        "deviceorientation",
        function(evt) {
          if (
            initialOffset === null &&
            evt.absolute !== true &&
            +evt.webkitCompassAccuracy > 0 &&
            +evt.webkitCompassAccuracy < 50
          ) {
            initialOffset = evt.webkitCompassHeading || 0;
          }

          alpha = evt.alpha - initialOffset;
          if (alpha < 0) {
            alpha += 360;
          }

          // Now use our derived world-based `alpha` instead of raw `evt.alpha` value
        },
        false
      );
    }

    return alpha;

    // const offsetDistance = 1;
    // if (alpha )
    // dn =
    // de =
    // dLat =
  }

  console.log(offsetGPS());
</script>

<style>
  #clear-btn {
    position: fixed;
    left: 50%;
    bottom: 10%;
    transform: translate(-50%, -50%);
    width: 50%;
    font-size: 1rem;
    z-index: 2;
  }

  #add-gps-ar {
    position: fixed;
    left: 50%;
    bottom: 15%;
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
  <!-- <a-sphere
    radius="1.25"
    scale="2 2 2"
    gps-entity-place="latitude:-33.938278;longitude:151.199671" /> -->
  <a-camera gps-camera="alert:true;" rotation-reader />
</a-scene>

<button id="add-gps-ar" on:click={addGPSBox}>Add GPS Box</button>

<button id="clear-btn" on:click={clearBox}>Clear Box</button>
