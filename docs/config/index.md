import { useState, useEffect } from "react";
import { BrowserRouter, Routes, Route, Link, useParams } from "react-router-dom";

/* ==================== DATOS SIMULADOS ==================== */
const products = [
  { id: "1", name: "Camiseta", category: "ropa", price: 30, description: "Camiseta 100% algodón", image: "https://via.placeholder.com/200x200?text=Camiseta" },
  { id: "2", name: "Pantalón", category: "ropa", price: 50, description: "Pantalón de mezclilla", image: "https://via.placeholder.com/200x200?text=Pantalón" },
  { id: "3", name: "Auriculares", category: "electrónica", price: 80, description: "Auriculares inalámbricos", image: "https://via.placeholder.com/200x200?text=Auriculares" },
  { id: "4", name: "Reloj", category: "accesorios", price: 120, description: "Reloj de acero inoxidable", image: "https://via.placeholder.com/200x200?text=Reloj" },
];

/* ==================== COMPONENTE NAVBAR ==================== */
const NavBar = () => {
  return (
    <nav style={styles.nav}>
      <Link to="/" style={styles.link}>Inicio</Link>
      <Link to="/category/ropa" style={styles.link}>Ropa</Link>
      <Link to="/category/electrónica" style={styles.link}>Electrónica</Link>
      <Link to="/category/accesorios" style={styles.link}>Accesorios</Link>
    </nav>
  );
};

/* ==================== COMPONENTE ITEMCOUNT ==================== */
const ItemCount = ({ stock, initial }) => {
  const [count, setCount] = useState(initial);

  const increment = () => count < stock && setCount(count + 1);
  const decrement = () => count > 1 && setCount(count - 1);

  return (
    <div style={styles.countContainer}>
      <button onClick={decrement} style={styles.button}>-</button>
      <span style={styles.counter}>{count}</span>
      <button onClick={increment} style={styles.button}>+</button>
      <button style={styles.addButton}>Agregar al carrito</button>
    </div>
  );
};

/* ==================== COMPONENTES DE PRESENTACIÓN ==================== */
const Item = ({ product }) => (
  <div style={styles.card}>
    <img src={product.image} alt={product.name} style={styles.image} />
    <h3>{product.name}</h3>
    <p>${product.price}</p>
    <Link to={`/item/${product.id}`} style={styles.detailButton}>Ver detalle</Link>
  </div>
);

const ItemList = ({ items }) => (
  <div style={styles.grid}>
    {items.map((p) => (
      <Item key={p.id} product={p} />
    ))}
  </div>
);

const ItemDetail = ({ product }) => (
  <div style={styles.detailContainer}>
    <img src={product.image} alt={product.name} style={styles.detailImage} />
    <div>
      <h2>{product.name}</h2>
      <p>{product.description}</p>
      <h3>Precio: ${product.price}</h3>
      <ItemCount stock={10} initial={1} />
    </div>
  </div>
);

/* ==================== CONTENEDORES ==================== */
const ItemListContainer = () => {
  const [items, setItems] = useState([]);
  const { categoryId } = useParams();

  useEffect(() => {
    const getProducts = new Promise((resolve) => {
      setTimeout(() => {
        resolve(categoryId ? products.filter(p => p.category === categoryId) : products);
      }, 600);
    });
    getProducts.then((res) => setItems(res));
  }, [categoryId]);

  return (
    <div style={styles.container}>
      <h2>{categoryId ? `Categoría: ${categoryId}` : "Catálogo de Productos"}</h2>
      <ItemList items={items} />
    </div>
  );
};

const ItemDetailContainer = () => {
  const [product, setProduct] = useState(null);
  const { itemId } = useParams();

  useEffect(() => {
    const getProduct = new Promise((resolve) => {
      setTimeout(() => {
        resolve(products.find((p) => p.id === itemId));
      }, 600);
    });
    getProduct.then((res) => setProduct(res));
  }, [itemId]);

  return (
    <div style={styles.container}>
      {product ? <ItemDetail product={product} /> : <p>Cargando producto...</p>}
    </div>
  );
};

/* ==================== APP PRINCIPAL ==================== */
function App() {
  return (
    <BrowserRouter>
      <NavBar />
      <Routes>
        <Route path="/" element={<ItemListContainer />} />
        <Route path="/category/:categoryId" element={<ItemListContainer />} />
        <Route path="/item/:itemId" element={<ItemDetailContainer />} />
        <Route path="*" element={<h2 style={{ textAlign: 'center' }}>404 - Página no encontrada</h2>} />
      </Routes>
    </BrowserRouter>
  );
}

/* ==================== ESTILOS BÁSICOS ==================== */
const styles = {
  nav: {
    display: "flex",
    justifyContent: "center",
    gap: "20px",
    padding: "15px",
    backgroundColor: "#222",
  },
  link: {
    color: "white",
    textDecoration: "none",
    fontWeight: "bold",
  },
  container: {
    padding: "30px",
    textAlign: "center",
  },
  grid: {
    display: "grid",
    gridTemplateColumns: "repeat(auto-fit, minmax(200px, 1fr))",
    gap: "20px",
    marginTop: "20px",
  },
  card: {
    border: "1px solid #ddd",
    borderRadius: "10px",
    padding: "15px",
    backgroundColor: "#fff",
  },
  image: {
    width: "100%",
    borderRadius: "10px",
  },
  detailButton: {
    display: "inline-block",
    marginTop: "10px",
    padding: "8px 15px",
    backgroundColor: "#333",
    color: "white",
    borderRadius: "5px",
    textDecoration: "none",
  },
  detailContainer: {
    display: "flex",
    justifyContent: "center",
    alignItems: "center",
    gap: "30px",
    flexWrap: "wrap",
  },
  detailImage: {
    width: "300px",
    borderRadius: "10px",
  },
  countContainer: {
    marginTop: "20px",
  },
  button: {
    padding: "8px 12px",
    margin: "0 5px",
  },
  addButton: {
    display: "block",
    marginTop: "10px",
    padding: "10px 20px",
    backgroundColor: "#28a745",
    color: "white",
    border: "none",
    borderRadius: "5px",
    cursor: "pointer",
  },
  counter: {
    fontWeight: "bold",
    fontSize: "18px",
  },
};

export default App;

