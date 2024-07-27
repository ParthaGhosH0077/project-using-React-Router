# project-using-React-Router
using React Router and NavLink, Links and Nested routes
# React Application with Protected Routes and Item Details Navigation

This React application demonstrates how to implement protected routes, handle incorrect routes, and navigate to item details pages using `react-router-dom`.

## Problem Statement

1. **Error Handling for Incorrect Routes**:
   - For all incorrect routes, display the `NotFound` page.
   - On the `NotFound` page, clicking the "Back to Home" button should navigate the user back to the Home page.

2. **Secure Pages with Protected Routes**:
   - Use the `Navigate` element of `react-router-dom` to navigate the user to the Home page if the login status is false.

3. **Item Details Navigation**:
   - Refactor the list route such that on index, it displays the `List` component and on the nested route `:itemId`, it displays the `ItemDetails` component.
   - Add a link element to navigate the user to the item details page around the name of each item in the `List` component.

## Solution

### Error Handling for Incorrect Routes

- Implemented the `NotFound` component to display a "Page not found" message and a "Back to Home" button.
- Configured the route to show the `NotFound` component for all incorrect routes.

### Securing Pages with Protected Routes

- Implemented the `ProtectedRoute` component to check the login status and navigate the user to the Home page if they are not logged in.
- Wrapped the `Contact`, `List`, and `ItemDetails` routes with the `ProtectedRoute` component.

### Item Details Navigation

- Refactored the `List` route to display the `List` component at the `/list` path and the `ItemDetails` component at the `/list/:itemId` path.
- Added `Link` elements around item names in the `List` component to navigate to the item details pages.

## Project Structure

src/
├── components/
│ ├── Navbar.js
│ ├── NotFound.js
│ └── ProtectedRoute.js
├── pages/
│ ├── Contact.js
│ ├── Home.js
│ ├── ItemDetails.js
│ └── List.js
├── data/
│ ├── itemListData.js
│ └── itemDetailsData.js
├── App.js
├── index.js
└── styles.css

markdown
Copy code

## Running the Application

### Prerequisites

- Node.js and npm installed on your machine.

### Steps

1. **Clone the repository**:

   ```bash
   git clone <repository-url>
   cd <repository-directory>
Install dependencies:

bash
Copy code
npm install
Run the application:

bash
Copy code
npm start
Open the application in your browser:

bash
Copy code
http://localhost:3000
Component Details
NotFound.js
javascript
Copy code
import { useNavigate, Navigate } from "react-router-dom";

export const NotFound = () => {
  const navigate = useNavigate();
  const handleBack = () => {
    navigate("/");
  };

  return (
    <div className="not-found page">
      <h3>Page not found.</h3>
      <img
        src="https://cdn-icons-png.flaticon.com/512/2748/2748558.png"
        alt="not found"
      />
      <button onClick={handleBack}>Back to Home</button>
    </div>
  );
};

export const ProtectedRoute = ({ isLoggedIn, children }) => {
  if (!isLoggedIn) {
    return <Navigate to="/" replace />;
  }
  return children;
};
App.js
javascript
Copy code
import "./styles.css";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import { Navbar } from "./components/Navbar";
import { Home } from "./pages/Home";
import { List } from "./pages/List";
import { Contact } from "./pages/Contact";
import { ItemDetails } from "./pages/ItemDetails";
import { NotFound, ProtectedRoute } from "./pages/NotFound";
import { useState } from "react";

export default function App() {
  const [isLoggedIn, setIsLoggedIn] = useState(true);

  const router = createBrowserRouter([
    {
      path: "/",
      errorElement: <NotFound />,
      element: <Navbar />,
      children: [
        {
          index: true,
          element: <Home loggedIn={isLoggedIn} setLoggedin={setIsLoggedIn} />
        },
        {
          path: "/contact",
          element: (
            <ProtectedRoute isLoggedIn={isLoggedIn}>
              <Contact />
            </ProtectedRoute>
          )
        },
        {
          path: "/list",
          children: [
            {
              index: true,
              element: (
                <ProtectedRoute isLoggedIn={isLoggedIn}>
                  <List />
                </ProtectedRoute>
              )
            },
            {
              path: ":itemId",
              element: (
                <ProtectedRoute isLoggedIn={isLoggedIn}>
                  <ItemDetails />
                </ProtectedRoute>
              )
            }
          ]
        }
      ]
    }
  ]);

  return (
    <div className="App">
      <RouterProvider router={router} />
    </div>
  );
}
List.js
javascript
Copy code
import { Link } from "react-router-dom";
import { itemListData } from "../data"; // Assume this is your data

export const List = () => {
  return (
    <div className="list page">
      <h3>Item List</h3>
      <ul>
        {itemListData.map((item) => (
          <li key={item.id}>
            <Link to={`/list/${item.id}`}>{item.name}</Link>
          </li>
        ))}
      </ul>
    </div>
  );
};
ItemDetails.js
javascript
Copy code
import { useParams } from "react-router-dom";
import { itemDetailsData } from "../data"; // Assume this is your data

export const ItemDetails = () => {
  const { itemId } = useParams();
  const item = itemDetailsData.find((i) => i.id === itemId);

  if (!item) {
    return <h3>Item details not found.</h3>;
  }

  return (
    <div className="item-details page">
      <h3>{item.name}</h3>
      <img src={item.image} alt={item.name} />
      <ul className="specs">
        {item.specs.map((spec, i) => (
          <li key={i}>{spec}</li>
        ))}
      </ul>
      <p>{item.description}</p>
    </div>
  );
};
