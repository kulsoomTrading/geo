<script>
  import { onMount } from "svelte";

  function loadPlaces(coords) {
    const PLACES = [
      {
        name: "Test Location",
        location: {
          lat: 53.224697,
          lng: -4.127461
        },
        src: "./images/Tree.glb"
      },
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

            const model = document.createElement("a-entity");
            model.setAttribute(
              "gps-entity-place",
              `latitude: ${latitude}; longitude: ${longitude}`
            );
            model.setAttribute("name", place.name);
            model.setAttribute("gltf-model", "url(./images/Tree.glb)");
            model.setAttribute("scale", "25 25 25");
            model.setAttribute('position', '0 -1.5 0');

            model.addEventListener("loaded", () =>
              window.dispatchEvent(new CustomEvent("gps-entity-place-loaded"))
            );

            const clickListener = function(ev) {
              ev.stopPropagation();
              ev.preventDefault();
              console.log('clicked')
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

            model.addEventListener("click", clickListener);

            scene.appendChild(model);

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

</script>

<style>
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
  <a-camera gps-camera="alert:true;" rotation-reader rotation-reader-2 />
</a-scene>
