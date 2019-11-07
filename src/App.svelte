<script>
  import { onMount } from "svelte";

  function loadPlaces(coords) {
    const PLACES = [
      {
        name: "Test Location",
        location: {
          lat: 53.224697,
          lng: -4.127461,
          src: "./images/tree.jpg"
        }
      },
      {
        name: "Test Location 2",
        location: {
          lat: -33.8616557,
          lng: 151.0833114,
          src: "./images/tree.jpg"
        }
      }
    ];

    return Promise.resolve(PLACES);
  }

  onMount(() => {
    const scene = document.querySelector("a-scene");

    return navigator.geolocation.getCurrentPosition(
      function(position) {
        loadPlaces(position.coords).then(places => {
          places.forEach(place => {
            const latitude = place.location.lat;
            const longitude = place.location.lng;

            const icon = document.createElement("a-image");
            icon.setAttribute(
              "gps-entity-place",
              `latitude: ${latitude}; longitude: ${longitude}`
            );
            icon.setAttribute("name", place.name);
            //icon.setAttribute("value", place.name);
            icon.setAttribute("width", "20");
            icon.setAttribute("src", place.src);
            icon.setAttribute("scale", "10, 10");
            icon.addEventListener("loaded", () =>
              window.dispatchEvent(new CustomEvent("gps-entity-place-loaded"))
            );

            const clickListener = function(ev) {
              ev.stopPropagation();
              ev.preventDefault();

              const name = ev.target.getAttribute("name");

              const el =
                ev.detail.intersection && ev.detail.intersection.object.el;

              if (el && el === ev.target) {
                const label = document.createElement("span");
                const container = document.createElement("div");
                container.setAttribute("id", "place-label");
                label.innerText = name;
                container.appendChild(label);
                document.body.appendChild(container);

                setTimeout(() => {
                  container.parentElement.removeChild(container);
                }, 1500);
              }
            };

            icon.addEventListener("click", clickListener);

            scene.appendChild(icon);
          });
        });
      },

      err => console.error("Error in retrieving position", err),
      {
        enableHighAccuracy: true,
        maximumAge: 0,
        timeout: 27000
      }
    );
  });

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

  // AFRAME.registerComponent("rotation-reader-2", {
  //   init: function() {},
  //   tick: function() {
  //     var quaternion = new THREE.Quaternion();
  //     //this.el.object3D.getWorldPosition(position);
  //     let sq = this.el.object3D.getWorldQuaternion(quaternion);
  //     var rotation = new THREE.Euler().setFromQuaternion(quaternion);
  //     //console.log(rotation);
  //   }
  // });
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

  #place-label {
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
    width: 70%;
    z-index: 3;
    background-color: white;
    color: black;
  }
</style>

<a-scene
  embedded
  arjs="sourceType: webcam; sourceWidth:1280; sourceHeight:960; displayWidth:
  1280; displayHeight: 960; debugUIEnabled: false;"
  cursor="rayOrigin: mouse; fuse: true; fuseTimeout: 0;"
  vr-mode-ui="enabled: false">
  <!-- <-- <a-sphere
    radius="1.25"
    scale="2 2 2"
    gps-entity-place="latitude:-33.938278;longitude:151.199671" /> -->
  <a-camera gps-camera="alert:true;" rotation-reader rotation-reader-2 />
</a-scene>

<!-- <button id="add-gps-ar" on:click={addGPSBox}>Add GPS Box</button>

<button id="clear-btn" on:click={clearBox}>Clear Box</button> -->
