

# Aframe AR GPS Demo

To test it, simply git clone the project, then npm install for dependencies.

```bash
npm install
```
To run the app, 

```bash
npm run dev
```

Navigate to [localhost:5000](http://localhost:5000) to view the app

# Replace model
1. Go to public/images and place your model in there.
2. Edit the relative path to the model file in App.svelte
```javascript
 const PLACES = [
    {
      name: "Test Location",
      location: {
        lat: 53.224697,
        lng: -4.127461
      },
      src: "./images/Tree.glb" //edit this to the new model name
    },
  ];

```