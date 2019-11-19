---
layout: post
title:      "Filter Items in an Index page in React"
date:       2019-11-19 03:10:55 +0000
permalink:  filter_items_in_an_index_page_in_react
---

class PetsContainer extends Component {
  state = {
    petsToDisplay: "all"
  };

  catState = () => {
    this.setState({ petsToDisplay: "cats" });
  };

  dogState = () => {
    this.setState({ petsToDisplay: "dogs" });
  };

  allPetsState = () => {
    this.setState({ petsToDisplay: "all" });
  };

  render() {
    const pets = this.props.pets;
    const dogs = pets.filter(pet => pet.species === "Dog");
    const cats = pets.filter(pet => pet.species === "Cat");
    let petsToShow = pets;
    if (this.state.petsToDisplay === "all") {
      petsToShow = pets;
    }
    if (this.state.petsToDisplay === "cats") {
      petsToShow = cats;
    }
    if (this.state.petsToDisplay === "dogs") {
      petsToShow = dogs;
    }

    return (
      <>
        <FilterButtons
          pets={pets}
          dogs={dogs}
          cats={cats}
          dogState={this.dogState}
          catState={this.catState}
          allPetsState={this.allPetsState}
        />
        <PetGallery petsToShow={petsToShow} dogs={dogs} cats={cats} />
      </>
    );
  }
}

