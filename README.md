# Chrome Browser Snippets / Firefox Scratchpad / Bookmarklets for Developer

This is a collection of usefull 🚀💪 / unuseful 🙈 Snippets for JS developer
that you can run on any page by using the Chrome snippets toolbar.

### 👩‍🎨 Turn on Designmode

you can turn on design mode to manipulate for example text or add an icon on a
page.

`document.designMode = 'on';`


###  [![jquery logo](<img src="https://simpleicons.org/icons/jquery.svg" width="24">)] Add jQuery

```
javascript: (function(e, s) {
    e.src = s;
    e.onload = function() {
        jQuery.noConflict();
        console.log('jQuery injected');
    };
    document.head.appendChild(e);
})(document.createElement('script'), '//code.jquery.com/jquery-latest.min.js')
```

### 📏 Add FPS meter

Add a JS Performance Monitor [More infos](https://github.com/mrdoob/stats.js/)

```
javascript: (function() {
    var script = document.createElement('script');
    script.onload = function() {
        var stats = new Stats();
        document.body.appendChild(stats.dom);
        requestAnimationFrame(function loop() {
            stats.update();
            requestAnimationFrame(loop)
        });
    }
    ;
    script.src = '//rawgit.com/mrdoob/stats.js/master/build/stats.min.js';
    document.head.appendChild(script);
}
)()
```

### 💩 Fartscroll

After running this snippet you can scroll through the page and your webpage will
fart. [More infos](https://github.com/theonion/fartscroll.js)

```
javascript: (function() {

    var script = document.createElement('script');

    script.onload = function() {
        fartscroll();
    };

    script.src = 'http://code.onion.com/fartscroll.js';
    document.head.appendChild(script);
}
)()
```

### 💅 Get Critical CSS

After running this snippet it prints out critical CSS.
[More infos](https://gist.github.com/PaulKinlan/6284142)

```
(function() {
  var CSSCriticalPath = function(w, d, opts) {
    var opt = opts || {};
    var css = {};
    var pushCSS = function(r) {
      if(!!css[r.selectorText] === false) css[r.selectorText] = {};
      var styles = r.style.cssText.split(/;(?![A-Za-z0-9])/);
      for(var i = 0; i < styles.length; i++) {
        if(!!styles[i] === false) continue;
        var pair = styles[i].split(": ");
        pair[0] = pair[0].trim();
        pair[1] = pair[1].trim();
        css[r.selectorText][pair[0]] = pair[1];
      }
    };

    var parseTree = function() {
      // Get a list of all the elements in the view.
      var height = w.innerHeight;
      var walker = d.createTreeWalker(d, NodeFilter.SHOW_ELEMENT, function(node) { return NodeFilter.FILTER_ACCEPT; }, true);

      while(walker.nextNode()) {
        var node = walker.currentNode;
        var rect = node.getBoundingClientRect();
        if(rect.top < height || opt.scanFullPage) {
          var rules = w.getMatchedCSSRules(node);
          if(!!rules) {
            for(var r = 0; r < rules.length; r++) {
              pushCSS(rules[r]);
            }
          }
        }
      }
    };

    this.generateCSS = function() {
      var finalCSS = "";
      for(var k in css) {
        finalCSS += k + " { ";
        for(var j in css[k]) {
          finalCSS += j + ": " + css[k][j] + "; ";
        }
        finalCSS += "}\n";
      }

      return finalCSS;
    };

    parseTree();
  };


  var cp = new CSSCriticalPath(window, document);
  var css = cp.generateCSS();

  console.log(css);
})();
```

## 🚅 Show Perfmap

This snippet generates a perfmap. [More infos](https://zeman.github.io/perfmap)

```
javascript:(function(){var el=document.createElement('script');el.src='https://zeman.github.io/perfmap/perfmap.js';document.body.appendChild(el);})();
```

## ⚒ Remove all CSS from current site

If the currently visited site uses jQuery you can easily run this snippet.
Otherwise run first [jQuery Snippet](#add-jquery) to inject jQuery to the
currently visited page and [More infos](https://zeman.github.io/perfmap)

```
javascript: (function() {
    jQuery('[style]').removeAttr('style');
    jQuery('link[type="text/css"]').remove();
    jQuery('style').remove();
}
)();
```

## ⏰ Timings

```
(function(window) {
    "use strict";
    window.timing = window.timing || {
        getTimes: function(opts) {
            var performance = window.performance || window.webkitPerformance || window.msPerformance || window.mozPerformance;
            if (performance === undefined) {
                return false
            }
            var timing = performance.timing;
            var api = {};
            opts = opts || {};
            if (timing) {
                if (opts && !opts.simple) {
                    for (var k in timing) {
                        if (isNumeric(timing[k])) {
                            api[k] = parseFloat(timing[k])
                        }
                    }
                }
                if (api.firstPaint === undefined) {
                    var firstPaint = 0;
                    if (window.chrome && window.chrome.loadTimes) {
                        firstPaint = window.chrome.loadTimes().firstPaintTime * 1e3;
                        api.firstPaintTime = firstPaint - window.chrome.loadTimes().startLoadTime * 1e3
                    } else if (typeof window.performance.timing.msFirstPaint === "number") {
                        firstPaint = window.performance.timing.msFirstPaint;
                        api.firstPaintTime = firstPaint - window.performance.timing.navigationStart
                    }
                    if (opts && !opts.simple) {
                        api.firstPaint = firstPaint
                    }
                }
                api.loadTime = timing.loadEventEnd - timing.fetchStart;
                api.domReadyTime = timing.domComplete - timing.domInteractive;
                api.readyStart = timing.fetchStart - timing.navigationStart;
                api.redirectTime = timing.redirectEnd - timing.redirectStart;
                api.appcacheTime = timing.domainLookupStart - timing.fetchStart;
                api.unloadEventTime = timing.unloadEventEnd - timing.unloadEventStart;
                api.lookupDomainTime = timing.domainLookupEnd - timing.domainLookupStart;
                api.connectTime = timing.connectEnd - timing.connectStart;
                api.requestTime = timing.responseEnd - timing.requestStart;
                api.initDomTreeTime = timing.domInteractive - timing.responseEnd;
                api.loadEventTime = timing.loadEventEnd - timing.loadEventStart
            }
            return api
        },
        printTable: function(opts) {
            var table = {};
            var data = this.getTimes(opts) || {};
            Object.keys(data).sort().forEach(function(k) {
                table[k] = {
                    ms: data[k],
                    s: +(data[k] / 1e3).toFixed(2)
                }
            });
            console.table(table)
        },
        printSimpleTable: function() {
            this.printTable({
                simple: true
            })
        }
    };
    function isNumeric(n) {
        return !isNaN(parseFloat(n)) && isFinite(n)
    }
    if (typeof module !== "undefined" && module.exports) {
        module.exports = window.timing
    }
}
)(typeof window !== "undefined" ? window : {});
timing.printTable();
```

### 👓 Tota11y Accessibility

[More infos](khan.github.io/tota11y/tota11y/build/tota11y)

```
javascript: (function() {
    var tota11y = document.createElement('SCRIPT');
    tota11y.type = 'text/javascript';
    tota11y.src = '//khan.github.io/tota11y/tota11y/build/tota11y.min.js';
    document.getElementsByTagName('head')[0].appendChild(tota11y);
}
)();
```
### 🏹 Active Element logger

logs the active Element which got selected by clicking on a node. 

```js
javascript:(function(){if(window._activeElInterval){clearInterval(window._activeElInterval);delete window._activeElInterval;}else{var activeEl;window._activeElInterval=setInterval(function(){var currentActiveEl=document.activeElement;if(currentActiveEl!==activeEl){activeEl=currentActiveEl;console.log(activeEl);}},200);}})();
```

### 🌏 Print global object keys

prints global objects which are not included in default browser window. 

```js

(function(){
    // create a blank iframe
    const iframe = document.createElement('iframe');
    // append it to the body
    document.body.append(iframe);
    //grab its window object
    const virgin = Object.keys(iframe.contentWindow);

    current = Object.keys(window);
    // filter for ones that arent in the blank virgin iframe
    const added = current.filter(key => !virgin.includes(key));

    console.log(added);
})()
```

### 🎨Print used Colors on a site

prints used colors in a console in a nice style.


```js
// allcolors.js
// https://github.com/bgrins/devtools-snippets
// Print out CSS colors used in elements on the page.

(function () {
  // Should include colors from elements that have a border color but have a zero width?
  var includeBorderColorsWithZeroWidth = false;

  var allColors = {};
  var props = ["background-color", "color", "border-top-color", "border-right-color", "border-bottom-color", "border-left-color"];
  var skipColors = {
    "rgb(0, 0, 0)": 1,
    "rgba(0, 0, 0, 0)": 1,
    "rgb(255, 255, 255)": 1
  };

  [].forEach.call(document.querySelectorAll("*"), function (node) {
    var nodeColors = {};
    props.forEach(function (prop) {
      var color = window.getComputedStyle(node, null).getPropertyValue(prop),
        thisIsABorderProperty = (prop.indexOf("border") != -1),
        notBorderZero = thisIsABorderProperty ? window.getComputedStyle(node, null).getPropertyValue(prop.replace("color", "width")) !== "0px" : true,
        colorConditionsMet;

      if (includeBorderColorsWithZeroWidth) {
        colorConditionsMet = color && !skipColors[color];
      } else {
        colorConditionsMet = color && !skipColors[color] && notBorderZero;
      }

      if (colorConditionsMet) {
        if (!allColors[color]) {
          allColors[color] = {
            count: 0,
            nodes: []
          };
        }

        if (!nodeColors[color]) {
          allColors[color].count++;
          allColors[color].nodes.push(node);
        }

        nodeColors[color] = true;
      }
    });
  });

  function rgbTextToRgbArray(rgbText) {
    return rgbText.replace(/\s/g, "").match(/\d+,\d+,\d+/)[0].split(",").map(function(num) {
      return parseInt(num, 10);
    });
  }

  function componentToHex(c) {
    var hex = c.toString(16);
    return hex.length == 1 ? "0" + hex : hex;
  }

  function rgbToHex(rgbArray) {
    var r = rgbArray[0],
      g = rgbArray[1],
      b = rgbArray[2];
    return "#" + componentToHex(r) + componentToHex(g) + componentToHex(b);
  }

  var allColorsSorted = [];
  for (var i in allColors) {
    var rgbArray = rgbTextToRgbArray(i);
    var hexValue = rgbToHex(rgbArray);

    allColorsSorted.push({
      key: i,
      value: allColors[i],
      hexValue: hexValue
    });
  }

  allColorsSorted = allColorsSorted.sort(function (a, b) {
    return b.value.count - a.value.count;
  });

  var nameStyle = "font-weight:normal;";
  var countStyle = "font-weight:bold;";
  function colorStyle(color) {
    return "background:" + color + ";color:" + color + ";border:1px solid #333;";
  };

  console.group("Total colors used in elements on the page: " + window.location.href + " are " + allColorsSorted.length);
  allColorsSorted.forEach(function (c) {
    console.groupCollapsed("%c    %c " + c.key + " " + c.hexValue + " %c(" + c.value.count + " times)",
      colorStyle(c.key), nameStyle, countStyle);
    c.value.nodes.forEach(function (node) {
      console.log(node);
    });
    console.groupEnd();
  });
  console.groupEnd("All colors used in elements on the page");

})();

```

### 🐛Debug css

Run [Debucsser](https://github.com/lucagez/Debucsser) hold the control key (⌃) and hover over elements or outline all elements by holding the ⌃ + ⇧ key.

```js
// Scroll down for Debucsser configurations
// check out repo and docs at:
// https://github.com/lucagez/Debucsser

class Debucsser {
  constructor(props) {
    this.config = props || {};
    this.color = this.config.color || 'palevioletred';
    this.width = this.config.width || '3px';
    this.style = this.config.style || 'solid';
    this.customClass = this.config.customClass || null;
    this.grayscaleOnDebug = this.config.grayscaleOnDebug || false;
    this.grayscaleOnDebugAll = this.config.grayscaleOnDebugAll || false;
    this.string = `${this.width} ${this.style} ${this.color}`;
    this.mainKey = this.config.mainKey || 17;
    this.secondKey = this.config.secondKey || 16;
    this.init = this.init.bind(this);
    this.debug = this.debug.bind(this);
    this.debugAll = this.debugAll.bind(this);
    this.stop = this.stop.bind(this);
    this.addClass = this.addClass.bind(this);
    this.labels = this.labels.bind(this);
    this.createGlobalClass = this.createGlobalClass.bind(this);
    this.removeGlobalClass = this.removeGlobalClass.bind(this);
  }
  init() {
    // initialize invisible label element => we'll make it visible on selected keystroke
    this.label = document.createElement('div');
    this.label.classList.add('debucsser-label');
    this.label.style = 'display: none;';
    document.body.appendChild(this.label);

    this.inject_label_style();
    this.createDebugStyle();
    this.debug();
    this.globalStyle = this.createGlobalClass();
  }
  debug() {
    console.log(this);
    document.addEventListener('keydown', (key) => {
      if (key.keyCode == this.mainKey) {
        document.addEventListener('mousemove', this.labels, true);
        document.addEventListener('mouseover', this.addClass, true);
        document.addEventListener('keydown', this.debugAll, true);
      }
      this.stop();
    });
  }
  stop() {
    document.addEventListener('keyup', (key) => {
      if (key.keyCode == this.mainKey) {
        document.removeEventListener('mouseover', this.addClass, true);
        document.removeEventListener('mousemove', this.labels, true);
        this.label.style = 'display: none;';
      }
    })
  }
  addClass(over) {
    over.target.classList.add(this.customClass ? this.customClass : 'debucsser');
    document.addEventListener('mouseout', (out) => {
      out.target.classList.remove(this.customClass ? this.customClass : 'debucsser');
    }, true);
  }
  debugAll(key) {
    if (key.keyCode == this.secondKey) {
      document.body.appendChild(this.globalStyle);
      document.addEventListener('keyup', this.removeGlobalClass, true)
    }
  }
  createDebugStyle() {
    const style = document.createElement('style');
    style.innerHTML = `
      .debucsser {
        outline: ${this.string};
        ${this.config.grayscaleOnDebug && 'filter: grayscale(100%);'}
      }
    `;
    document.body.appendChild(style);
  }
  createGlobalClass() {
    const global = document.createElement('style');
    console.log(this.config.grayscaleOnDebugAll)
    global.innerHTML = `
      * {
        outline: ${this.string};
        ${this.config.grayscaleOnDebugAll && 'filter: grayscale(100%);'}
      }
    `;
    return global;
  }
  removeGlobalClass(key) {
    if (key.keyCode == this.secondKey) {
      document.body.removeChild(this.globalStyle);
    }
  }
  labels(e) {
    if (e.target) {
      const classList = e.target.classList ? e.target.classList.value.replace('debucsser', '') : undefined;
      const id = e.target.id ? '#' + e.target.id : undefined;
      const dimensions = e.target.getBoundingClientRect();
      this.label.innerHTML = `
        <h2>class: <strong>${classList || `¯\\_(ツ)_/¯`}</strong></h2>
        <h2>id: <strong>${id || `¯\\_(ツ)_/¯`}</strong></h2>
        <h2><strong>${dimensions.width.toFixed(0)}px</strong> × <strong>${dimensions.height.toFixed(0)}px</strong></h2>
      `;
      this.label.style = `display: block; top:${e.clientY + 20}px; left:${e.clientX + 20}px;`;
    } else {
      this.label.style = 'display: none;';
    }
  }
  inject_label_style() {
    const style = document.createElement('div');
    style.innerHTML = `
    <style>
      .debucsser-label {
        position: fixed; 
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif, "Apple Color Emoji", "Segoe UI Emoji", "Segoe UI Symbol";
        padding: 10px 20px; 
        background: #333; 
        border-radius: 3px;
        color: #f9f9f9; 
        opacity: 0.9;
        z-index: 999;
      }
      .debucsser-label strong {
        color: palevioletred;
      }
    </style>`;
    document.body.appendChild(style);
  }
}

// init Debucsser

// comment out config properties to customize how Debucsser behave
const config = {
    // color: 'palevioletred',
    // width: '4px',
    // grayscaleOnDebugAll: true,
    // customClass: 'testShadow',
    }
const debug = new Debucsser(config).init();


// demo initial explanation
const explain = document.querySelector('.explain');
explain.querySelector('button').addEventListener('click', () => {
  explain.style.transform = 'translateX(2000px)';
  document.querySelector('.wrapper').style.filter = 'none';
});
```

## Similiar Projects
https://github.com/bgrins/devtools-snippets
