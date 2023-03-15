<template>
  <div className="main">
    <h1 className="main-title">Recipe Book</h1>

    <div className="new-recipe-form">
      <input type="text" className="recipe-title-input" placeholder="Add a new recipe..." v-model="newRecipe"
        @keyup.enter="addRecipes" />
      <button className="recipe-create-button" @click="addRecipes">Add</button>
    </div>

    
    {/* Exemple de recettes */}

    <div className="recipe-book" v-for="(recipe, index) in recipes" :key="index">
      <div className="recipe">
        <div className="recipe-title">{{ recipe.name }}</div>
        <div className="recipe-actions">
          <button className="recipe-edit-button" @click="editRecipes(index)">Edit</button>
          <button className="recipe-delete-button" @click="deleteRecipes(index)">Delete</button>
        </div>
      </div>
    </div>

    <div v-for="(recipe, index) in recipes" :key="index">
      <div v-if="recipe.editing">
        <div className="recipe-edit-form">
          <input type="text" className="recipe-edit-title-input" v-model="recipe.name" />
          <textarea rows="10" className="recipe-edit-description-input" v-model="recipe.resume"></textarea>
          <h2 className="recipe-ingredients-title">Ingredients</h2>
          <ul className="recipe-ingredients-list">
            <li className="recipe-ingredient" v-for=" (ingredient, i) in recipe.ingredients" :key="i">
              <input type="text" className="recipe-ingredient-input" v-model="ingredient.name" />
              <button className="recipe-ingredient-delete-button" @click="deleteIngredients(index, i)">🗑</button>
            </li>
            <div className="recipe-new-ingredient">
              <input type="text" className="recipe-new-ingredient-input" placeholder="Add a new ingredient..."
                v-model="newIngredients" @keyup.enter="addIngredients(index)" />
              <button className="recipe-new-ingredient-button" @click="addIngredients(index)">Add</button>
            </div>
          </ul>
          <div className="recipe-edit-actions">
            <button className="recipe-edit-save-button" @click="saveRecipes(index)">Save</button>
            <button className="recipe-edit-cart-button" @click="addIngredientsToShoppingList()">Add to shopping
              list</button>
          </div>
        </div>
      </div>
    </div>

    {/* Exemple de Shopping liste*/}
    <div className="shopping-list">
      <h2 className="shopping-list-title">Shopping list</h2>
      <ul className="recipe-ingredients-list">
        <li className="recipe-ingredient" v-for="(sList, index) in sLists" :key="index">
          <label className="shopping-list-item"><input type="checkbox" v-model="sList.completed" />{{ sList.name }}</label>
        </li>
      </ul>
      <div className="shopping-list-actions">
        <button className="shopping-list-clear-button" @click="completeAllSLists">Check all</button>
        <button className="shopping-list-clear-button" @click="deleteAllSListsCompleted">Clear checked items</button>
        <button className="shopping-list-clear-button" @click="deleteAllSLists">Clear all</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      recipes: [
        { name: 'Winter vegetables soup', resume: ' A delicious soup made with winter vegetables. Its a great way to use up leftover vegetables. To prepare this soup, you will need a large pot, a knife, a cutting board, a spoon, a ladle, and a vegetable peeler. Peel the carrots and the potatoes. Cut the carrots and the poftatoes into small cubes. Cut the onion into small cubes. Cut the celery into small cubes. Cut the leek into small cubes. Cut the garlic into small cubes....', ingredients: [{ name: '1 onion' }, { name: '2 patates' }, { name: '3 carottes' }], editing: false },
        { name: 'Pizza de la mama', resume: ' Découvrez les pizzas du grand chef Delamama, un savoir-faire perpétré de génération en génération, et ce depuis près de 25 ans. Le petit prince de la pizza s’invite aujourd’hui dans votre congélateur. 13 à 15 minutes au four, et le voyage peut commen.', ingredients: [{ name: '1 pate' }, { name: '2 sauce tomates' }, { name: '1 champignons' }], editing: false },
        { name: 'Turkish Kebab', resume: '  made of meat cooked on a vertical rotisserie. Seasoned meat stacked in the shape of an inverted cone is turned slowly on the rotisserie, next to a vertical cooking element. The operator uses a knife to slice thin shavings from the outer layer of the meat as it cooks. The vertical rotisserie', ingredients: [{ name: '2 doner' }, { name: '3kg viande' }, { name: '1 tomate' }], editing: false }
      ],
      newRecipe: '',
      newIngredients: '',
      sLists: [{ name: '1 onions', completed: false, }, { name: '2 carrots', completed: true, }, { name: '3 potatoes', completed: false, }]
    };
  },
  methods: {


    //ajouter une recette 
    addRecipes() {
      if (this.newRecipe.trim() === '') {
        return;
      }
      this.recipes.push({ name: this.newRecipe, resume: '', ingredients: [], editing: 'false' });
      this.newRecipe = '';
    },



    //ajouter un ingrédient
    addIngredients(index) {
      if (this.newIngredients.trim() === '') {
        return;
      }
      this.recipes[index].ingredients.push({ name: this.newIngredients });
      this.newIngredients = '';
    },

    //supprimer une recette
    deleteRecipes(index) {
      this.recipes.splice(index, 1);
    },

    //Supprimer un ingrédient
    deleteIngredients(recipeIndex, ingredientIndex) {
      this.recipes[recipeIndex].ingredients.splice(ingredientIndex, 1);
    },


    //Afficher et modifier une recette
    editRecipes(index) {
      this.recipes.forEach((recipe) => {
        recipe.editing = false;
      });
      this.recipes[index].editing = true;
    },

    //Sauvegarder et quitter la recette
    saveRecipes(index) {
      //titre obligatoire
      if (this.recipes[index].name.trim() === '') {
        return;
      }
      this.recipes[index].editing = false;
    },


    //Ajouter les ingrédients dans la liste Shopping
    addIngredientsToShoppingList() {
      const index = this.recipes.findIndex(recipe => recipe.editing);
      this.recipes[index].ingredients.forEach(ingredient => {
        this.sLists.push({
          name: ingredient.name,
          completed: false
        });
      });
    },

    //Effacer toute la liste Shopping
    deleteAllSLists() {
      this.sLists = [];
    },

    //Effacer seulement les produits cochés
    deleteAllSListsCompleted() {
      this.sLists = this.sLists.filter(sList => !sList.completed);
    },

    // Coché tout les produits
    completeAllSLists() {
      this.sLists.forEach((sList) => {
        sList.completed = true;
      });
    },
  }
};
</script>


<style>
body {
  margin: 0;
  display: flex;
  place-items: center;
  min-width: 320px;
  min-height: 100vh;
  background-color: #ebf4ff;
}

button {
  border-radius: 8px;
  border: 1px solid transparent;
  padding: 0.6rem 1.2rem;
  font-size: 1rem;
  font-weight: 500;
  font-family: inherit;
  background-color: #1e3041;
  color: #fff;
  cursor: pointer;
  transition: all 0.25s;
  outline: none;
}

button:hover {
  background-color: #45627d;
}

button:focus-visible {
  box-shadow: inset 0 0 0 2px #45627d, inset 0 0 0 4px #6589ab;
}

input[type="checkbox"] {
  appearance: none;
  background-color: transparent;
  border: 1px solid #1e3041;
  border-radius: 4px;
  width: 1.2rem;
  height: 1.2rem;
  cursor: pointer;
  transition: all 0.25s;
}

input[type="checkbox"]:checked {
  background-color: #1e3041;
}

.main {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  min-height: 100vh;
  overflow-y: auto;
  color: #1e3041;
  font-family: 'Roboto', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Oxygen',
    'Ubuntu', 'Cantarell', 'Fira Sans', 'Droid Sans', 'Helvetica Neue',
    sans-serif;
}

.main-title {
  font-size: 3rem;
  font-weight: bold;
  margin: 0 0 2rem;
  text-shadow: 0 -1px 1px #415c71;
}

.new-recipe-form {
  display: flex;
  align-items: stretch;
  gap: 0.5rem;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.recipe-title-input {
  font-size: 1.5rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
}

.recipe-create-button {
  outline: none;
  border: none;
  font-size: 1.5rem;
  background-color: #1e3041;
  color: #fff;
  transition: background-color 0.2s ease-in-out;
}

.recipe-create-button:hover {
  background-color: #415c71;
}

.recipe-book {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.recipe {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  padding: 1rem;
}

.recipe:not(:last-child) {
  border-bottom: 1px solid #ececec;
}

.recipe-title {
  font-size: 1.5rem;
  font-weight: 500;
}

.recipe-actions {
  display: flex;
  gap: 0.5rem;
}

.recipe-edit-button {
  outline: none;
  border-color: #1e3041;
  background-color: transparent;
  color: #1e3041;
  transition: all 0.2s ease-in-out;
}

.recipe-edit-button:hover {
  border-color: #45627d;
  background-color: transparent;
  color: #45627d;
}

.recipe-delete-button {
  outline: none;
  border: none;
  transition: all 0.2s ease-in-out;
}

.recipe-edit-form {
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
}

.recipe-edit-title-input {
  display: block;
  font-size: 1.5rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
  width: 100%;
  box-sizing: border-box;
}

.recipe-edit-description-input {
  display: block;
  font-size: 1rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 1rem;
  color: #1e3041;
  width: 100%;
  box-sizing: border-box;
}

.recipe-ingredients-title {
  font-size: 1.2rem;
  font-weight: 800;
  margin: 0;
  padding: 1rem;
}

.recipe-ingredients-list {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  gap: 0.5rem;
  padding: 0 1rem 1rem;
  margin: 0;
}

.recipe-ingredient,
.recipe-new-ingredient {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 0.5rem;
  padding: 0.5rem;
  border-radius: 0.5rem;
  background-color: #ececec;
}

.recipe-ingredient-input,
.recipe-new-ingredient-input {
  flex: 1;
  font-size: 1rem;
  font-weight: 500;
  border: none;
  outline: none;
  background-color: transparent;
  padding: 0.5rem;
  color: #1e3041;
}

.recipe-new-ingredient {
  align-items: stretch;
  padding: 0;
}

.recipe-new-ingredient-input {
  padding: 1rem;
}

.recipe-edit-save-button,
.recipe-edit-cart-button {
  flex: 1;
}

.recipe-ingredient-delete-button {
  opacity: 0;
  outline: none;
  border-color: #1e3041;
  background-color: transparent;
  color: #1e3041;
  transition: all 0.2s ease-in-out;
  padding: 0.3rem 0.5rem;
}

.recipe-ingredient-delete-button:hover {
  border-color: #45627d;
  background-color: transparent;
  color: #45627d;
}

.recipe-ingredient:hover .recipe-ingredient-delete-button {
  opacity: 1;
}

.recipe-edit-actions {
  display: flex;
  gap: 0.5rem;
  padding: 1rem;
}

.shopping-list {
  display: flex;
  flex-direction: column;
  align-items: stretch;
  min-width: 400px;
  max-width: 800px;
  width: 100%;
  border-radius: 0.5rem;
  background-color: #fff;
  box-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.1);
  margin-bottom: 2rem;
}

.shopping-list-title {
  font-size: 1.2rem;
  font-weight: 800;
  margin: 0;
  padding: 1rem;
}

.shopping-list-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  flex: 1;
  cursor: pointer;
}

.shopping-list-actions {
  display: flex;
  gap: 0.5rem;
  padding: 1rem;
}</style>