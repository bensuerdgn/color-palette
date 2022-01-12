<template>
  <div class="color-palette">
    <h1 class="color-palette-title">Color Palette 🎨</h1>
    <div class="box-containers">
      <div class="box-container">
        <div
          class="box primary-dark"
          id="primary-dark"
          @click="copied('primary-dark')"
        >
          {{ this.hex.primary[2] }}
        </div>
        <div
          class="box primary-medium"
          id="primary-medium"
          @click="copied('primary-medium')"
        >
          {{ this.hex.primary[1] }}
        </div>
        <div
          class="box primary-light"
          id="primary-light"
          @click="copied('primary-light')"
        >
          {{ this.hex.primary[0] }}
        </div>
        <div
          class="box primary-complement"
          id="primary-complement"
          @click="copied('primary-complement')"
        >
          {{ this.hex.primary[3] }}
        </div>
        <div
          class="box primary-complement-1"
          id="primary-complement-1"
          @click="copied('primary-complement-1')"
        >
          {{ this.hex.primary[4] }}
        </div>
        <div
          class="box primary-complement-2"
          id="primary-complement-2"
          @click="copied('primary-complement-2')"
        >
          {{ this.hex.primary[5] }}
        </div>
      </div>
      <div class="box-container">
        <div
          class="box secondary-dark"
          id="secondary-dark"
          @click="copied('secondary-dark')"
        >
          {{ this.hex.secondary[2] }}
        </div>
        <div
          class="box secondary-medium"
          id="secondary-medium"
          @click="copied('secondary-medium')"
        >
          {{ this.hex.secondary[0] }}
        </div>
        <div
          class="box secondary-light"
          id="secondary-light"
          @click="copied('secondary-light')"
        >
          {{ this.hex.secondary[1] }}
        </div>
        <div
          class="box secondary-complement"
          id="secondary-complement"
          @click="copied('secondary-complement')"
        >
          {{ this.hex.secondary[3] }}
        </div>
        <div
          class="box secondary-complement-1"
          id="secondary-complement-1"
          @click="copied('secondary-complement-1')"
        >
          {{ this.hex.secondary[4] }}
        </div>
        <div
          class="box secondary-complement-2"
          id="secondary-complement-2"
          @click="copied('secondary-complement-2')"
        >
          {{ this.hex.secondary[5] }}
        </div>
      </div>
    </div>
    <div class="progress">
      <input type="button" @click="progressUpDown('up')" value="Light" />
      <progress id="file" :value="progress" max="100">{{ progress }}</progress>
      <input type="button" @click="progressUpDown('down')" value="Dark" />
    </div>
    <div class="color-picker">
      <div class="color-picker-input">
        <input
          type="color"
          id="primary-color-input"
          v-model="primaryColor"
          @change="setTheme(primaryColor, 'primary')"
        />
        <label for="primary-color-input">Base Color</label>
      </div>
      <div class="color-picker-input">
        <input
          type="color"
          id="secondary-color-input"
          v-model="secondaryColor"
          @change="setTheme(secondaryColor, 'secondary')"
        />
        <label for="secondary-color-input">Base Color</label>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ColorPalette",
  data() {
    return {
      primaryColor: "#6400f0",
      secondaryColor: "#018989",
      hex: { primary: [], secondary: [] },
      progress: 50,
      lightness: 5,
      mediumness: 10,
      darkness: 15,
      h_value: 0,
      s_value: 0,
      l_value: 0,
    };
  },

  methods: {
    setTheme(H, inputType) {
      let r = 0,
        g = 0,
        b = 0;
      if (H.length == 4) {
        r = "0x" + H[1] + H[1];
        g = "0x" + H[2] + H[2];
        b = "0x" + H[3] + H[3];
      } else if (H.length == 7) {
        r = "0x" + H[1] + H[2];
        g = "0x" + H[3] + H[4];
        b = "0x" + H[5] + H[6];
      }
      // Then to HSL
      r /= 255;
      g /= 255;
      b /= 255;
      let cmin = Math.min(r, g, b),
        cmax = Math.max(r, g, b),
        delta = cmax - cmin,
        h = 0,
        s = 0,
        l = 0;

      if (delta == 0) h = 0;
      else if (cmax == r) h = ((g - b) / delta) % 6;
      else if (cmax == g) h = (b - r) / delta + 2;
      else h = (r - g) / delta + 4;

      h = Math.round(h * 60);

      if (h < 0) h += 360;

      l = (cmax + cmin) / 2;
      s = delta == 0 ? 0 : delta / (1 - Math.abs(2 * l - 1));
      s = +(s * 100).toFixed(1);
      l = +(l * 100).toFixed(1);

      var root = document.documentElement.style;

      root.setProperty(`--${inputType}-color-h`, h);
      root.setProperty(`--${inputType}-color-s`, s + "%");
      root.setProperty(`--${inputType}-color-l`, l + "%");

      /////////////////////////////////////////////

      var style = getComputedStyle(document.body);
      //var regex = /\d+ \+ \d+|\d+(.\d)?/gm;
      // var regex =
      //   /\d+(.\d)? (\+|\-) \d+(.\d)?|\d+(.\d)?|\d+(.\d)? |(\+|\-)  \d+(.\d)?/gm;
      // var regex =
      //   /\d+(.\d)? \D \d+(.\d)?|\d+(.\d)?|\d+(.\d)? |\D\s\s\d+(.\d)?/gm;
      var regex =
        /\d+(.\d)? [\\+\\-] \d+(.\d)?|\d+(.\d)?|\d+(.\d)? |[\\+\\-]\s+[\\-]?\d+(.\d)?/gm;
      var boxes = [
        `--color-${inputType}-light`,
        `--color-${inputType}-medium`,
        `--color-${inputType}-dark`,
        `--color-${inputType}-complement`,
        `--color-${inputType}-complement-1`,
        `--color-${inputType}-complement-2`,
      ];
      var _this = this;
      boxes.forEach((element) => {
        var hsl = style.getPropertyValue(element, "hsl");
        var regexResult = [...hsl.matchAll(regex)];
        console.log(hsl);
        // if (inputType == "primary") {
        //   this.hex.primary.push(hsl);
        // } else {
        //   this.hex.secondary.push(hsl);
        // }
        // console.log(hsl);
        // var result = [...hsl.matchAll(regex)];
        // console.log(result);

        if (hsl.match(/calc/gi).length == 2) {
          var h_value_array = regexResult[0][0].split(" ");
          if (h_value_array[1] == "+") {
            _this.h_value =
              parseInt(h_value_array[0]) + parseInt(h_value_array[2]);
          } else {
            _this.h_value =
              parseInt(h_value_array[0]) - parseInt(h_value_array[2]);
          }
          _this.s_value = parseInt(regexResult[1][0]);
          var l_value_array = regexResult[3][0].split(" ");
          if (l_value_array[0] == "+") {
            if (l_value_array[2] == undefined) {
              _this.l_value =
                parseInt(regexResult[2][0]) + parseInt(l_value_array[1]);
            } else {
              _this.l_value =
                parseInt(regexResult[2][0]) + parseInt(l_value_array[2]);
            }
          } else {
            if (l_value_array[2] == undefined) {
              _this.l_value =
                parseInt(regexResult[2][0]) - parseInt(l_value_array[1]);
            } else {
              _this.l_value =
                parseInt(regexResult[2][0]) - parseInt(l_value_array[2]);
            }
          }
        } else if (hsl.search("calc") == 6) {
          _this.h_value = 0;
          _this.s_value = 0;
          _this.l_value = 0;
        } else {
          _this.h_value = parseInt(regexResult[0][0]);
          _this.s_value = parseInt(regexResult[1][0]);
          var l_value_array2 = regexResult[3][0].split(" ");
          if (l_value_array2[0] == "+") {
            if (l_value_array2[2] == undefined) {
              _this.l_value =
                parseInt(regexResult[2][0]) + parseInt(l_value_array2[1]);
            } else {
              _this.l_value =
                parseInt(regexResult[2][0]) + parseInt(l_value_array2[2]);
            }
          } else {
            if (l_value_array2[2] == undefined) {
              _this.l_value =
                parseInt(regexResult[2][0]) - parseInt(l_value_array2[1]);
            } else {
              _this.l_value =
                parseInt(regexResult[2][0]) - parseInt(l_value_array2[2]);
            }
          }
        }
        console.log(_this.h_value, _this.s_value, _this.l_value);
        this.hslToHex(this.h_value, _this.s_value, _this.l_value, inputType);
      });
    },

    hslToHex(h, s, l, inputType) {
      l /= 100;

      const a = (s * Math.min(l, 1 - l)) / 100;
      const f = (n) => {
        const k = (n + h / 30) % 12;
        const color = l - a * Math.max(Math.min(k - 3, 9 - k, 1), -1);
        return Math.round(255 * color)
          .toString(16)
          .padStart(2, "0"); // convert to Hex and prefix "0" if needed
      };
      if (inputType == "primary") {
        return this.hex.primary.push(`#${f(0)}${f(8)}${f(4)}`);
      } else {
        return this.hex.secondary.push(`#${f(0)}${f(8)}${f(4)}`);
      }
    },
    copied(id) {
      //      <span class="tooltip">Copied</span>

      var copyText = document.getElementById(id);
      navigator.clipboard.writeText(copyText.innerText);

      var newSpan = document.createElement("span");
      newSpan.className = "tooltip";
      var newContent = document.createTextNode("Copied");
      newSpan.appendChild(newContent);
      copyText.appendChild(newSpan);
      setTimeout(() => {
        copyText.children[0].remove();
      }, 1000);
      //alert("copied : " + copyText.innerText);
    },
    progressUpDown(input) {
      var root = document.documentElement.style;

      if (input == "up") {
        if (this.progress > 0) {
          this.progress -= 5;
          this.lightness += 1;
          this.mediumness += 1;
          this.darkness += 1;
          root.setProperty("--lightnessTransform", this.lightness + "%");
          root.setProperty("--mediumnessTransform", this.mediumness + "%");
          root.setProperty("--darknessTransform", this.darkness + "%");
          this.hex = { primary: [], secondary: [] };

          this.setTheme(this.primaryColor, "primary");
          this.setTheme(this.secondaryColor, "secondary");
        }
      } else {
        if (this.progress < 100) {
          this.progress += 5;
          this.lightness -= 1;
          this.mediumness -= 1;
          this.darkness -= 1;
          root.setProperty("--lightnessTransform", this.lightness + "%");
          root.setProperty("--mediumnessTransform", this.mediumness + "%");
          root.setProperty("--darknessTransform", this.darkness + "%");
          this.hex = { primary: [], secondary: [] };

          this.setTheme(this.primaryColor, "primary");
          this.setTheme(this.secondaryColor, "secondary");
        }
      }
    },
  },
  mounted() {
    this.hex = { primary: [], secondary: [] };

    this.setTheme(this.primaryColor, "primary");
    this.setTheme(this.secondaryColor, "secondary");
  },
};
</script>


<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">
@import "@/assets/style.scss";
</style>
