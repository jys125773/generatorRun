```
 function run(generator) {
      const task = generator();
      let result = task.next();

      function step() {
        if (!result.done) {
          if (result.value && typeof result.value.then === 'function') {
            result.value.then(
              (data) => {
                result = task.next(data);
                step();
              },
              (err) => {
                result = task.throw(err);
                step();
              }
            );
          } else if (result.value && typeof result.value === 'function') {
            result.value((err, data) => {
              if (err) {
                result = task.throw(err);
                step();
              } else {
                result = task.next(data);
                step();
              }
            });
          } else {
            result = task.next(result.value);
            step();
          }
        }
      }

      step();
    }
    
function fetchBlob(url) {
      return function (callback) {
        const xhr = new XMLHttpRequest();
        xhr.open('get', url, true);
        xhr.responseType = 'blob';

        xhr.onload = function () {
          if (this.status == 200 || this.status == 304) {
            const blob = this.response;
            callback(null, blob);
          }
        }
        xhr.onerror = function (err) {
          callback(err);
        };
        xhr.send(null);
      }
    }


    function* generator2() {
      let a = yield 1;
      console.log(a);
      let b = yield a + 2;
      console.log(b);
      let imageBlob = yield fetchBlob('https://cn.vuejs.org/images/logo.png');
      console.log(imageBlob)
    }

    function* generator1() {
      let a = yield 1;
      console.log(a);
      let b = yield a + 2;
      console.log(b);
      let imageBlob = yield fetch('https://cn.vuejs.org/images/logo.png').then(response => response.blob());
      console.log(imageBlob)
    }
```