https://stackoverflow.com/questions/49631688/property-dataset-does-not-exist-on-type-eventtarget
e.target.dataset이 안되는 이유는 이벤트 타겟은 Element, document, window가 될 수 있기 때문이다. 이를 방지하기 위해 EventTarget이 Element인지 확인해야 한다. 
```jsx
linkProvider = (ev: React.SyntheticEvent<EventTarget>) => {
  // If event target not an HTMLButtonElement, exit
  if (!(ev.target instanceof HTMLButtonElement)) {
    return;
  }
  console.debug('ev.target', ev.target.dataset['ix']) 
}
```
```tsx
linkProvider = (ev: React.SyntheticEvent<HTMLButtonElement>) => {
  console.debug('ev.target', ev.currentTarget.dataset['ix']) 
}
```
