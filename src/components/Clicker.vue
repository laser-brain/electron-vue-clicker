<template>
  <div class="draggable-area fill">
    <div class="top-right">
      <button class="small round exit" v-on="{ click: exit }">X</button>
    </div>
    <div class="flex">
      <button class="round click" v-on="{ click: click }">INSULT!</button>
      <span>Count: {{ count }}</span>
      <input placeholder="enter your name" type="text" v-model="user" />
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

export default {
  name: 'Clicker',
  mounted() {
    window.api.send('toMain', { event: 'get-clicks' });
  },
  setup() {
    const user = ref(localStorage.getItem('user'));
    const count = ref(0);

    const exit = () => {
      localStorage.setItem('user', user.value);
      window.api.send('toMain', { event: 'exit' });
    };

    const click = () => {
      if (!user.value) {
        alert('You must set a user name!!');
        return;
      }

      localStorage.setItem('user', user.value);

      window.api.send('toMain', {
        event: 'add-click',
        data: { user: user.value },
      });
    };

    window.api.receive('fromMain', (data) => {
      count.value = data.clicks;
    });

    return {
      count,
      user,
      click,
      exit,
    };
  },
};
</script>

<style scoped lang="scss">
* {
  margin: 5px;
}

.draggable-area {
  -webkit-app-region: drag;
}

.fill {
  width: 100%;
  height: 100%;
}

.flex {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.top-right {
  position: absolute;
  top: 1%;
  right: 1%;
}

button,
input,
span {
  outline: none;
  border: none;
  background-color: black;
  color: white;
  -webkit-app-region: no-drag;
}

input {
  text-align: center;
  max-width: 150px;
  overflow: hidden;
}

button {
  cursor: pointer;
  &.small {
    width: 25px;
    height: 25px;
  }
  &.round {
    border-radius: 100%;
  }
  &.exit {
    background-color: gray;
  }
  &.click {
    margin-top: 20px;
    background: transparent;
    border: 2px solid white;
    color: white;
    height: 130px;
    width: 130px;
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    text-transform: uppercase;
    font-family: "Muli-LightItalic", Helvetica;
    font-size: 20px;
    line-height: 30px;
    animation: ripple 1.5s linear infinite;
    transition: all 0.7s ease;
  }

  &:hover {
    transform: scale(1.1);
  }

  &:focus {
    outline: none;
  }

  &:active {
    transform: scale(0.8);
  }
}

@keyframes ripple {
  0% {
    box-shadow: 0 0 0 0 rgba(50, 50, 60, 0.3), 0 0 0 1px rgba(50, 50, 60, 0.3),
      0 0 0 3px rgba(50, 50, 60, 0.3), 0 0 0 5px rgba(50, 50, 60, 0.3);
  }

  100% {
    box-shadow: 0 0 0 0 rgba(50, 50, 60, 0.3), 0 0 0 4px rgba(50, 50, 60, 0.3),
      0 0 0 20px rgba(50, 50, 60, 0), 0 0 0 30px rgba(50, 50, 60, 0);
  }
}
</style>
