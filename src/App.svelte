<script>



function addGPSBox(){
	const scene = document.querySelector('a-scene')
	navigator.geolocation.getCurrentPosition((position)=>{	
		const latitude =  position.coords.latitude
		const longitude =  position.coords.longitude
		alert(latitude,longitude);
		const box = document.createElement('a-box');
		box.setAttribute('gps-entity-place',`latitude:${latitude}; longitude:${longitude}`);
		box.setAttribute('scale', '2 2 2');
		box.setAttribute('color', randomHsl())
		
		scene.appendChild(box)
	}, (err)=>console.log(err), {
		enableHighAccuracy: true,
		maximumAge: 0,
		timeout: 27000,
	})
}

const randomHsl = () => `hsla(${Math.random() * 360}, 100%, 50%, 1)`

function insertBox(){
	const scene = document.querySelector('a-scene')
	const box = document.createElement('a-box');
	box.setAttribute('ar-box','true');
	box.setAttribute('position','0 1.5 -4');
	box.setAttribute('scale', '1 1 1');
	box.setAttribute('color', randomHsl())
	scene.appendChild(box)
}

function clearBox(){
	const boxes = document.querySelectorAll('[ar-box]')
	boxes.forEach(box=>{
		box.parentNode.removeChild(box)
	})
}


</script>

<style>

#add-ar {
	position: fixed;
	left: 50%;
	bottom: 15%;
	transform: translate(-50%,-50%);
	width: 90%;
	font-size: 1rem;
	z-index: 2;
	display: flex;
}

#add-ar button {
	flex:1;
}

#add-gps-ar {
	position: fixed;
	left: 50%;
	bottom: 10%;
	transform: translate(-50%,-50%);
	width: 50%;
	font-size: 1rem;
	z-index: 2;
}

</style>


<a-scene embedded arjs='sourceType: webcam; sourceWidth:1280; sourceHeight:960; displayWidth: 1280; displayHeight: 960; debugUIEnabled: false;'cursor='rayOrigin: mouse; fuse: true; fuseTimeout: 0;'   vr-mode-ui="enabled: false">
	<!-- <a-box scale="2 2 2" id="box" gps-entity-place="latitude:-33.867664;longitude:151.0817792"></a-box> -->
	<a-camera gps-camera="minDistance: 20;" rotation-reader>
	</a-camera>
</a-scene>

<div id="add-ar">
<button on:click={insertBox}>Add Box</button>
<button on:click={clearBox}>Clear Box</button>
</div>

<button id="add-gps-ar" on:click={addGPSBox}>Add GPS Box</button>
