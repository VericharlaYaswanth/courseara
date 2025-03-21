// src/App.js
import React from 'react';
import { BrowserRouter as Router, Routes, Route } from 'react-router-dom';
import { Provider } from 'react-redux';
import { store } from './store';
import LandingPage from './pages/LandingPage';
import ProductListing from './pages/ProductListing';
import ShoppingCart from './pages/ShoppingCart';
import Header from './components/Header';

function App() {
  return (
    <Provider store={store}>
      <Router>
        <Header />
        <Routes>
          <Route path='/' element={<LandingPage />} />
          <Route path='/products' element={<ProductListing />} />
          <Route path='/cart' element={<ShoppingCart />} />
        </Routes>
      </Router>
    </Provider>
  );
}

export default App;

// src/components/Header.js
import React from 'react';
import { Link } from 'react-router-dom';
import { useSelector } from 'react-redux';

const Header = () => {
  const totalQuantity = useSelector(state => state.cart.totalQuantity);
  return (
    <header>
      <nav>
        <Link to='/'>Home</Link>
        <Link to='/products'>Products</Link>
        <Link to='/cart'>Cart ({totalQuantity})</Link>
      </nav>
    </header>
  );
};

export default Header;

// src/pages/LandingPage.js
import React from 'react';
import { Link } from 'react-router-dom';

const LandingPage = () => {
  return (
    <div className='landing-page'>
      <h1>Houseplant Haven</h1>
      <p>Your one-stop shop for beautiful indoor plants.</p>
      <Link to='/products'>
        <button>Get Started</button>
      </Link>
    </div>
  );
};

export default LandingPage;

// src/pages/ProductListing.js
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { addToCart } from '../cartSlice';

const plants = [
  { id: 1, name: 'Snake Plant', price: 15 },
  { id: 2, name: 'Peace Lily', price: 20 },
  { id: 3, name: 'Pothos', price: 12 },
  { id: 4, name: 'Monstera', price: 25 },
  { id: 5, name: 'ZZ Plant', price: 18 },
  { id: 6, name: 'Fiddle Leaf Fig', price: 30 }
];

const ProductListing = () => {
  const dispatch = useDispatch();
  const cartItems = useSelector(state => state.cart.items);

  return (
    <div className='product-listing'>
      {plants.map(plant => (
        <div key={plant.id} className='plant-item'>
          <h3>{plant.name}</h3>
          <p>${plant.price}</p>
          <button
            onClick={() => dispatch(addToCart(plant))}
            disabled={cartItems.some(item => item.id === plant.id)}
          >
            {cartItems.some(item => item.id === plant.id) ? 'Added' : 'Add to Cart'}
          </button>
        </div>
      ))}
    </div>
  );
};

export default ProductListing;

// src/pages/ShoppingCart.js
import React from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { increaseQuantity, decreaseQuantity, removeFromCart } from '../cartSlice';
import { Link } from 'react-router-dom';

const ShoppingCart = () => {
  const dispatch = useDispatch();
  const { items, totalQuantity, totalPrice } = useSelector(state => state.cart);

  return (
    <div className='shopping-cart'>
      <h2>Shopping Cart</h2>
      <p>Total Items: {totalQuantity}</p>
      <p>Total Price: ${totalPrice}</p>
      {items.map(item => (
        <div key={item.id} className='cart-item'>
          <h3>{item.name}</h3>
          <p>${item.price} x {item.quantity}</p>
          <button onClick={() => dispatch(increaseQuantity(item.id))}>+</button>
          <button onClick={() => dispatch(decreaseQuantity(item.id))}>-</button>
          <button onClick={() => dispatch(removeFromCart(item.id))}>Remove</button>
        </div>
      ))}
      <button>Checkout (Coming Soon)</button>
      <Link to='/products'>
        <button>Continue Shopping</button>
      </Link>
    </div>
  );
};

export default ShoppingCart;

// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './App';
import './styles.css';

ReactDOM.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
  document.getElementById('root')
);
