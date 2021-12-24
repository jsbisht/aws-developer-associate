```js
let minutes = 0,
  seconds = 0;
function setTimeInMinutes(str) {
  let tempM = minutes,
    tempS = seconds;
  if (str.indexOf(":")) {
    const [mins, secs] = str.split(":");
    tempM += parseInt(mins);
    if (tempS + secs >= 60) {
      tempM += parseInt((tempS + parseInt(secs)) / 60);
    } else {
      tempS += parseInt(secs);
    }
    if (!(isNaN(tempM) || isNaN(tempS))) {
      minutes = tempM;
      seconds = tempS;
    }
  }
}

document.querySelectorAll(".lecture-name").forEach((node) => {
  const text = node.innerText.trim();
  const time = text.substring(text.lastIndexOf("(") + 1, text.length - 1);
  setTimeInMinutes(time);
});

console.log(minutes / 60); // 66.16666666666667
```
