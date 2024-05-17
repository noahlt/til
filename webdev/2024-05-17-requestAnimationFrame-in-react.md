# requestAnimationFrame in React

Use this hook:

```ts
const useFrameTime = () => {
  const [frameTime, setFrameTime] = useState(performance.now());
  useEffect(() => {
    let frameId: number;
    const frame = (time: DOMHighResTimeStamp) => {
      setFrameTime(time);
      frameId = requestAnimationFrame(frame);
    };
    requestAnimationFrame(frame);
    return () => cancelAnimationFrame(frameId);
  }, []);
  return frameTime;
};
```

From [Writing a run loop in Javascript &amp; React](https://medium.com/projector-hq/writing-a-run-loop-in-javascript-react-9605f74174b) by Luke Millar. (I added types.)
