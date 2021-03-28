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
  async mounted() {
    await this.init();
  },
  setup() {
    const user = ref(localStorage.getItem('user'));
    const count = ref(0);
    const serverUri = '<your-server-uri-here>';

    const init = async () => {
      const initResponse = await fetch(`${serverUri}/api/click`);
      const initData = await initResponse.json();
      count.value = initData.clicks;
    };

    const click = async () => {
      if (!user.value) {
        alert('You must set a user name!');
        return;
      }

      localStorage.setItem('user', user.value);

      const response = await fetch(`${serverUri}/api/click`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json;charset=utf-8',
        },
        body: JSON.stringify({
          user: user.value,
        }),
      });
      const data = await response.json();
      count.value = data.clicks;
    };

    const exit = () => {
      localStorage.setItem('user', user.value);
      window.api.send('toMain', { event: 'exit' });
    };

    return {
      count,
      user,
      click,
      exit,
      init,
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
    transform: scale(.8);
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
