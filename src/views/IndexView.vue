<template>
  <template>
  <div class="title">
      <h1>Photo Filter App</h1>
  </div>
  <div class="flex-container">
      <div class="polaroid" v-show="camera_visible">
        <video
          autoplay
          muted
          playsInline
          v-bind="camera"
          width="640"
          height="480"
          id="cam"
        />
        <div class="container">
          <button class="button" @click="capture" >Capture</button>
        </div>
      </div>
      <div class="polaroid" v-show="canvas_visible">
        <canvas
          id="canvas"
          width="640"
          height="480"
          />
        <div class="container">

          <span v-for="filter in filters" :key="filter">
            <button @click="runFilter(filter)" class="button">{{filter}}</button>
          </span>

          <div>
            <button class="button-back" @click="back" >Back</button>
          </div>
        </div>
      </div>
  </div>
</template>
</template>
<script>
import { onMounted, reactive, ref } from 'vue';
import { applyPresetOnCanvas, clarendon, gingham, aden } from 'instagram-filters';

export default {
  name: 'App',
  setup(){
    const camera = reactive({
      srcObject : null,
      element : null,
    });

    const canvas_visible = ref(false);
    const camera_visible = ref(true);

    const filters = ref(['clarendon', 'gingham', 'aden']);

    const constraint = {
      audio : false,
      video : true,
    }

    const load = async() => {
      try{
        camera.srcObject = await navigator.mediaDevices.getUserMedia(constraint);
        camera.element = document.getElementById('cam');
      }
      catch(e)
      {
        console.log(e);
      }
    }

    onMounted(()=>{
      if(navigator.mediaDevices.getUserMedia||navigator.mediaDevices.webkitGetUserMedia)
      {
        load();
      }else{
        alert("Browser Not Supported");
      }
    });

    const canvas = {
      element : null,
      context : null,
    }

    const capture = () =>{
      canvas.element = document.getElementById("canvas");
      canvas.context = canvas.element.getContext("2d");
      canvas.context.drawImage(camera.element,0,0,640,480);

      canvas_visible.value = true;
      camera_visible.value = false;
    }

    const back = () =>{
      canvas_visible.value = false;
      camera_visible.value = true;
    }

    const runFilter = (filter) =>{
      switch(filter){
        case "clarendon":
          applyPresetOnCanvas(canvas.element, clarendon());
          break;
        case "gingham":
          applyPresetOnCanvas(canvas.element, gingham());
          break;
        case "aden":
          applyPresetOnCanvas(canvas.element, aden());
          break;
      }
    }

    return {
      camera,
      capture,
      canvas_visible,
      camera_visible,
      back,
      filters,
      runFilter
    }
  }
}
</script>
