<!-- RoomCountBox.vue -->
<template>
    <div class="room-count-box">
      <p class="room-count-text">{{ roomCount }} </p>
      <div class="room-count-label">Pieces</div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      roomCount: {
        type: Number,
        required: true,
      },
    },
  };
  </script>
  
  <style scoped>
  .room-count-box {
    position: absolute;
    top: 3%;
    left: 7%;
    width: 170px;
    height: 150px;
    background-color: #ca8a03;
    backdrop-filter: blur(10px);
    border: 1px solid #ca8a03;
    box-shadow: 0 0 40px rgba(8,7,16,0.6);
    border-radius: 14%;
    padding: 15px;
    box-sizing: border-box;
    
  }
  
  .room-count-text {
    margin-top:6px;
    text-align: center;
    font-size: 35px;
    font-weight: bold;
    color: #fcfcfc;
  }
  .room-count-label {
    text-align: center;
    font-size: 18px;
    font-weight: bold;
    color: #fcfcfc;
  }
  </style>