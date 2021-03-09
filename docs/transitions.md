# Transitions
To transition between scenes, you first need to create a transition and set it as output.
```JavaScript
const transition = osn.TransitionFactory.create(transitionType, "myTransition", {});
transition.set(scene);
osn.Global.setOutputSource(0, transition);
```
You can set the scene using the `transition.set(scene)` method and transition to another scene like this:
```JavaScript
transition.start(300, scene);
```
The `start` method needs the duration of the transition and the scene object to transition to.
