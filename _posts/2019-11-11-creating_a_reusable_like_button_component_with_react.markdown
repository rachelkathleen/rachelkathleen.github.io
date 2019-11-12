---
layout: post
title:      "Creating a reusable 'Like Button' Component with React"
date:       2019-11-11 22:20:51 -0500
permalink:  creating_a_reusable_like_button_component_with_react
---

<iframe src="https://share.getcloudapp.com/eDuLKkYb?embed=true" width="575" height="400" style="border:none" frameborder="0" allowtransparency="true" allowfullscreen="true"></iframe>

This is a step-by-step guide on how to create a component in react that can be used as a 'like' or counter button.

*  Create a class component that renders a button:

```
class LikeButton extends React.Component {

  render() {
	
	  return <button>A button!</button>
}
```

That was easy!

* Set an initial state for 'likes' that starts at 0 and update the button to display the current state.

```
class LikeButton extends React.Component {

  state = {
    likes: 0
  };

  render() {
	
	  return <button>Likes: {this.state.likes} </button>
}
```

* Create a click handler that will increment the number of likes each time the button is clicked.

```
addLike = () => {
    let newCount = this.state.likes + 1;
     this.setState({
     likes: newCount
     });
  };
```

Update the button to have use the click handler: `return <button onClick={this.addLike}>Likes: {this.state.likes} </button>`

*  If you are adding a button to multiple items - a gallery of photos, or a list of things, and want to count 'likes' for each individually follow these additional steps.
    *  Be sure each item in your index/gallery/list has a unique key in it's parent html element (div, li etc) - `key={item.id}`
    *  Pass that id in as a prop when you are rendering the button component - `<LikeButton itemId={item.id} /> `
    *  Add the id to the button - `<button onClick={this.addLike} id={this.props.itemId}>Likes: {this.state.likes} </button>`

And you're done! See a working example with added styling in this [CodePen](https://codepen.io/rachelhawa/pen/VwwdNQz).


 
 
 










