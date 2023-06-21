# E-COMMERCE SHOPPING APPLICATION USING REACT, SPRINGBOOT AND SQL
## AIM:
To develop a E-commerce shopping application using react,springboot and sql.
## ALGORITHM:
1. Create a springboot project.
2. Add neccessary java files to the project.
3. Connect it with a database.
4. Now create the front end using react.
5. Create required components in react project.
6. Run the project.

## PROGRAM:
### SPRINGBOOT PROGRAM:
### Product.java:
```
package com.saveetha.product.pro;

import jakarta.persistence.*;

import java.time.LocalDate;
import java.time.Period;

@Entity
@Table
public class Product {
    @Id
    @SequenceGenerator(
            name="productSequence",
            sequenceName = "productSequence",
            allocationSize = 1
    )
    @GeneratedValue(
            strategy = GenerationType.SEQUENCE,
            generator = "productSequence"
    )
    private Long productID;
    private String productName;
    private String customerName;
    private Long productPrice;

    public Product() {
    }

    public product(String productName,String customerName,Long productPrice) {
        this.productName = productName;
        this.customerName = customerName;
        this.productPrice = productPrice;
    }


    public Long getproductID() {
        return productID;
    }
    public String getproductName() {
        return productName;
    }
    public String getcustomerName() {
        return customerName;
    }
    public Long getproductPrice() {
        return productPrice;
    }

    public void setproductID(Long productID) {
        this.productID = productID;
    }
    public void setproductName(String productName) {
        this.productName = productName;
    }
    public void setcustomerName(Long customerName) {
        this.customerName = customerName;
    }
    public void setproductPrice(Long productPrice) {
        this.productPrice = productPrice;
    }

   

    @Override
    public String toString() {
        return "Product{" +
                "productId=" + productID +
                ", productName='" + productName + '\'' +
                ", customerName=" + customerName +
                ", productPrice=" + productPrice + '\'' +
                '}';
    }
}

```
### ProductController.java
```
package com.saveetha.product.pro;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.*;


import java.util.List;

@RestController
@RequestMapping(path="/api/v1/product")
public class ProductController {
    private final ProductService productService;
    @Autowired
    public ProductController(ProductService productService) {
        this.productService = productService;
    }



    @GetMapping("/")
    public List<Product> getProductDetails(){
        return productService.displayProductDetails();
    }

    @PostMapping("/")
    public void postProduct(@RequestBody Product product){
        productService.registerNewProduct(product);
    }

    @DeleteMapping(path={"/{id}"})
    public void deleteProduct(@PathVariable("id") Long productId)
    {
        productService.removeProduct(productId);
    }
}

```

### REACT PROGRAM:
### App.js:
```
import React from "react"
import './App.css';
import { BrowserRouter as Router, Route, Routes, Link } from "react-router-dom";
import ProductRegistrationComponent from './components/ProductRegistrationComponent/ProductRegistrationComponent';
import HeaderComponent from "./components/HeaderComponent/HeaderComponent";
import ProductDirectoryComponent from "./components/ProductDirectoryComponent/ProductDirectoryComponent";
import ProductDeletionComponent from "./components/ProductDeletionComponent/ProductDeletionComponent";

function App() {
  return (
    <Router>
            <div className="container">
              <HeaderComponent/>
              
            <nav className="nav-menu">
                <Link to="/" >Product Details</Link>
                <Link to="/admin/add" >Add Product</Link>
                <Link to="/admin/delete" >Delete Product</Link>
            </nav>
           <Routes>
                 <Route exact path='/' element={<ProductDirectoryComponent/>}></Route>
                 <Route path='/admin/add' element={<ProductRegistrationComponent/>}></Route>
                 <Route path='/admin/delete' element={<ProductDeletionComponent/>}></Route>
          </Routes>
          </div>
       </Router>
  );
}

export default App;
```
### ProductDirectoryComponent.js
```
import React, { useEffect, useState } from 'react';
import './ProductDirectoryComponent.css'; 

function ProductDirectoryComponent() {
  const [product, setproduct] = useState([]);

  useEffect(() => {
    fetchProduct();
  }, []);

  const fetchProduct = async () => {
    try {
      const response = await fetch('http://localhost:8080/api/v1/product/');
      const data = await response.json();
      setProduct(data);
    } catch (error) {
      console.error('Error:', error);
    }
  };

  return (
    <div className="product-list">
      {products.map((product) => (
        <div className="product-card" key={product.productID}>
          <p>Product ID: {product.productID}</p>
          <p>Product Name: {product.productName}</p>
          <p>Customer Name: {product.customerName}</p>
          <p>Product Price: {product.productPrice}</p>
        </div>
      ))}
    </div>
  );
};

export default ProductDirectoryComponent;
```
### ProductRegistrationComponent.js
```
import React, { useState } from 'react';
import './ProductRegistrationComponent.css'

function ProductRegistrationComponent() {
  const [product, setProduct] = useState({
    productName: '',
    customerName: '',
    productPrice: ''
  });

  const handleChange = (event) => {
    const { name, value } = event.target;
    setProduct({ ...product, [name]: value });
  };

  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch('http://localhost:8080/api/v1/product/',{
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(product)
    })
        .then((response) => {
            if (response.status === 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data of ${product.productName} is added successfully`)
                window.location.href = '/'
            }
        })
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Product Name:
        <input
          type="text"
          name="productName"
          value={product.productName}
          onChange={handleChange}
          required
        />
      </label>
      <label>
        Customer Name:
        <input
          type="text"
          name="customerName"
          value={product.customerName}
          onChange={handleChange}
          required
        />
      </label>
      <label>
        Product Price:
        <input
          type="number"
          name="productPrice"
          value={product.productPrice}
          onChange={handleChange}
          required
        />
      </label>
      
      <button type="submit">Submit</button>
    </form>
  );
};

export default ProductRegistrationComponent;

```
### ProductDeletionComponent.js
```
import React, { useState } from 'react';
import './ProductDeletionComponent.css'

function ProductDeletionComponent() {
  const [productID, setProductID] = useState('');

  const handleChange = (event) => {
    setProductID(event.target.value);
  };

  const handleSubmit = async(event) => {
    event.preventDefault();
    await fetch(`http://localhost:8080/api/v1/product/${productID}`,{
      method: 'DELETE'
    })
    .then((response) => {
            if (response.status === 500)
            {
                alert(`Error!`)
            }
            else{
                alert(`Data deleted successfully`)
                window.location.href = '/'
            }
        })
  };

  return (
    <form onSubmit={handleSubmit}>
      <label>
        Product ID:
        <input
          type="number"
          name="productID"
          value={productID}
          onChange={handleChange}
          required
        />
      </label>
      <button type="submit">Submit</button>
    </form>
  );
};

export default ProductDeletionComponent;

```

## OUTPUT:
![image](https://github.com/Rithigasri/E-CommerceApplication/assets/93427256/4dad0942-6000-4a18-a1ea-fadcb65785fe)
![image](https://github.com/Rithigasri/E-CommerceApplication/assets/93427256/894a5c51-ed38-446a-8f8f-3978c628356d)

## RESULT:
Hence, E-commerce shopping application is developed using react, springboot and sql.
