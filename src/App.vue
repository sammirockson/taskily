<template>
   <div id="app" class="layout">
    <main class="main container">
      <RouterView/>
    </main>
    <PortalTarget name="popup"/>
  </div>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;0,800;0,900;1,400;1,500;1,600;1,700;1,800;1,900&display=swap');
:root {
  --color-primary: #EA7E41;
  --color-dark-primary: #1C2634;
  --color-light-primary: #6c7278;
  --color-primary-pink: #7380ec;
  --color-danger: #ff7782;
  --color-success:#41f1b6;
  --color-primary-green: #1dbb6c;
  --color-warning: #ffbb55;
  --color-info-dark: #7d8da1;
  --color-info-light: #dce1eb;
  --color-dark: #363949;
  --color-light: rgba(132, 139, 200, 0.18);
  --color-light-variant: rgba(255, 255, 255, 0.38);
  --color-primary-variant: #07db71;
  --color-dark-variant: #677483;
  --color-background: #f6f6f9;
  --color-card-background: white;
  --color-card-title: #1C2634;
  --color-light-yellow: #fff8df;

  /* FAFAFA f6f6f9 */
  --color-blue: #4d81E7;
  --color-light-orange: #FDEFD9;
  --color-dark-theme: #1A1C1E;
  --color-light-green: #12B3A8;
  --color-bar-dark: #262626;
  --color-light-pink: #f6f5ff;

  --card-border-radius: 2rem;
  --border-radius-1: 0.4rem;
  --border-radius-2: 0.8rem;
  --border-radius-3: 1.2rem;
  --card-padding: 1.8rem;
  --padding-1: 1.2rem;
  --box-shadow: 0 2rem 3rem var(--color-light);
}

/* #201c1c */
.dark-theme-variables {
  --color-background: #181414;
  --color-card-background: #111018;
  --color-dark-primary: white;
  --color-card-title: white;
  --color-dark: white;
}

.light {
  --font-color: #111;
  --bg-color: #fff;
}

.dark {
  --font-color: #eee;
  --bg-color: #111;
}

#app {
  font-family: 'Poppins', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  background-color: var(--color-background);
}

body, label, p, h1, h2, h3, h4, h5, v-btn, v-label {
  font-family: 'Poppins', Helvetica, Arial, sans-serif;
  color: var(--color-dark-primary);
}

body {
  overflow: hidden;
  overscroll-behavior: none;
  background-color: var(--color-background);
}

html, body {
  overscroll-behavior: none;
}

#app {
  background-color: transparent;
}

nav a {
  font-weight: bold;
  color: #2c3e50;
}

nav a.router-link-exact-active {
  color: #42b983;
}

// .dp__instance_calendar {
//   .dp__range_end, .dp__range_start, .dp__active_date {
//     color: var(--gold);
//     background: transparent;
//     border: 0px solid transparent;
//   }

//   .dp__today {
//     border: 0px solid transparent;
//   }
// }

// .dp__main {
//     // Helps date picker input align height with UI
//   .dp__input_wrap input {
//     height: var(--calendar-field-height);
//   }
//   .dp__menu_inner, .dp__instance_calendar {
//     background: #19242E;
//     border-radius: var(--border-radius-1);
//   }
//   .dp__overlay_container {
//     background: #19242E;
//   }
// }

</style>


<!-- https://github.com/Leecason/element-tiptap -->