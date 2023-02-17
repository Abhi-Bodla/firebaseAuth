> > Requirements for authenticating with Firebase in React

    1. Node.js installed
    2. Code editor — I prefer Visual Studio Code
    3. Google account — we need this to use Firebase
    4. Basic knowledge of React — I won’t recommend this tutorial for complete beginners in React

> > Setting up Firebase

    Firstly,  Click on Add project, and Enter Your Project Name and Continue!!
     After Completing all the steps you will be rendered to dashboard.

> > let’s set up Authentication

-- Click on Authentication on the sidebar and click on Get Started

    -- First click on Email/Password, enable it, and save it.

     -- Now press on Google, and Press enable, select a project support email address, and click on Save to activate Google Authentication for our app.

> > Create Database.

    -- Click on Cloud Firestore on the sidebar and click on Create Database.
    -- Remember to select Start in test mode.
    -- Click Next. Select the region, leave it to the default, and then press Enable.
    -- This should completely set up your Cloud Firestore database.

    >> CREATING AND SETTING UP A REACT APP

       * npx create-react-app your_app_name

       * cd your_app_name
       * npm start

       -- few commands to install a few npm packages

          * npm install firebase react-router-dom react-firebase-hooks

> > Integrating Firebase into our React app

    -- Go to your Firebase Console dashboard, click on Project Settings, scroll down, and you should see something like this (</>).
    -- Click on the icon (</>) to configure our Firebase project for the web. Enter the app name and click on Continue.
    -- Now go to the Project Setting and select the radiobutton for config.


    -- Copy the config. Create a new file in the src folder named firebase.js.

      Here, your firebase.js file should be look like this,


                                    import { initializeApp } from "firebase/app";
                                import {
                                GoogleAuthProvider,
                                getAuth,
                                signInWithPopup,
                                signInWithEmailAndPassword,
                                createUserWithEmailAndPassword,
                                sendPasswordResetEmail,
                                signOut,
                                } from "firebase/auth";
                                import {
                                getFirestore,
                                query,
                                getDocs,
                                collection,
                                where,
                                addDoc,
                                } from "firebase/firestore";
                                const firebaseConfig = {
                                apiKey: "AIzaSyDIXJ5YT7hoNbBFqK3TBcV41-TzIO-7n7w",
                                authDomain: "fir-auth-6edd8.firebaseapp.com",
                                projectId: "fir-auth-6edd8",
                                storageBucket: "fir-auth-6edd8.appspot.com",
                                messagingSenderId: "904760319835",
                                appId: "1:904760319835:web:44fd0d957f114b4e51447e",
                                measurementId: "G-Q4TYKH9GG7",
                                };
                                const app = initializeApp(firebaseConfig);
                                const auth = getAuth(app);
                                const db = getFirestore(app);
                                const googleProvider = new GoogleAuthProvider();
                                const signInWithGoogle = async () => {
                                try {
                                    const res = await signInWithPopup(auth, googleProvider);
                                    const user = res.user;
                                    const q = query(collection(db, "users"), where("uid", "==", user.uid));
                                    const docs = await getDocs(q);
                                    if (docs.docs.length === 0) {
                                    await addDoc(collection(db, "users"), {
                                        uid: user.uid,
                                        name: user.displayName,
                                        authProvider: "google",
                                        email: user.email,
                                    });
                                    }
                                } catch (err) {
                                    console.error(err);
                                    alert(err.message);
                                }
                                };
                                const logInWithEmailAndPassword = async (email, password) => {
                                try {
                                    await signInWithEmailAndPassword(auth, email, password);
                                } catch (err) {
                                    console.error(err);
                                    alert(err.message);
                                }
                                };
                                const registerWithEmailAndPassword = async (name, email, password) => {
                                try {
                                    const res = await createUserWithEmailAndPassword(auth, email, password);
                                    const user = res.user;
                                    await addDoc(collection(db, "users"), {
                                    uid: user.uid,
                                    name,
                                    authProvider: "local",
                                    email,
                                    });
                                } catch (err) {
                                    console.error(err);
                                    alert(err.message);
                                }
                                };
                                const sendPasswordReset = async (email) => {
                                try {
                                    await sendPasswordResetEmail(auth, email);
                                    alert("Password reset link sent!");
                                } catch (err) {
                                    console.error(err);
                                    alert(err.message);
                                }
                                };
                                const logout = () => {
                                signOut(auth);
                                };
                                export {
                                auth,
                                db,
                                signInWithGoogle,
                                logInWithEmailAndPassword,
                                registerWithEmailAndPassword,
                                sendPasswordReset,
                                logout,
                                };

                                In which, we create functions for Authentication.

> > Login Page

<!-- ---------------------------------------Login.js-------------------------------------------- -->

Go to Login.js, and let’s look at how our login functionality works:

                                <!-- import React, { useEffect, useState } from "react";
                                import { Link, useNavigate } from "react-router-dom";
                                import { auth, signInWithEmailAndPassword, signInWithGoogle } from "./firebase";
                                import { useAuthState } from "react-firebase-hooks/auth";
                                import "./Login.css";
                                function Login() {
                                const [email, setEmail] = useState("");
                                const [password, setPassword] = useState("");
                                const [user, loading, error] = useAuthState(auth);
                                const navigate = useNavigate();
                                useEffect(() => {
                                    if (loading) {
                                    // maybe trigger a loading screen
                                    return;
                                    }
                                    if (user) navigate("/dashboard");
                                }, [user, loading]);
                                return (
                                    <div className="login">
                                    <div className="login__container">
                                        <input
                                        type="text"
                                        className="login__textBox"
                                        value={email}
                                        onChange={(e) => setEmail(e.target.value)}
                                        placeholder="E-mail Address"
                                        />
                                        <input
                                        type="password"
                                        className="login__textBox"
                                        value={password}
                                        onChange={(e) => setPassword(e.target.value)}
                                        placeholder="Password"
                                        />
                                        <button
                                        className="login__btn"
                                        onClick={() => signInWithEmailAndPassword(email, password)}
                                        >
                                        Login
                                        </button>
                                        <button className="login__btn login__google" onClick={signInWithGoogle}>
                                        Login with Google
                                        </button>
                                        <div>
                                        <Link to="/reset">Forgot Password</Link>
                                        </div>
                                        <div>
                                        Don't have an account? <Link to="/register">Register</Link> now.
                                        </div>
                                    </div>
                                    </div>
                                );
                                }
                                export default Login; -->

<!-- ----------------------------------------------Login.css---------------------------------------------- -->

Go to Login.css and add the following styles.

                                        .login {
                                        height: 100vh;
                                        width: 100vw;
                                        display: flex;
                                        align-items: center;
                                        justify-content: center;
                                        }
                                        .login__container {
                                        display: flex;
                                        flex-direction: column;
                                        text-align: center;
                                        background-color: #dcdcdc;
                                        padding: 30px;
                                        }
                                        .login__textBox {
                                        padding: 10px;
                                        font-size: 18px;
                                        margin-bottom: 10px;
                                        }
                                        .login__btn {
                                        padding: 10px;
                                        font-size: 18px;
                                        margin-bottom: 10px;
                                        border: none;
                                        color: white;
                                        background-color: black;
                                        }
                                        .login__google {
                                        background-color: #4285f4;
                                        }
                                        .login div {
                                        margin-top: 7px;
                                        }

                                        Here’s what’s happening in the above code block. We are using the functions we made in firebase.js for authentication. We are also using react-firebase-hooks along with useEffect to track the authentication state of the user. So, if the user gets authenticated, the user will automatically get redirected to the dashboard.

> > Register Page
> > Create a new component called Register to handle user registrations.

<!-- ----------------------------------------Register.js-------------------------------------------- -->

let’s look at how the register functionality is implemented in the layout. Use this layout in Register.js

                                    import React, { useEffect, useState } from "react";
                                    import { useAuthState } from "react-firebase-hooks/auth";
                                    import { Link, useHistory } from "react-router-dom";
                                    import {
                                    auth,
                                    registerWithEmailAndPassword,
                                    signInWithGoogle,
                                    } from "./firebase";
                                    import "./Register.css";
                                    function Register() {
                                    const [email, setEmail] = useState("");
                                    const [password, setPassword] = useState("");
                                    const [name, setName] = useState("");
                                    const [user, loading, error] = useAuthState(auth);
                                    const history = useHistory();
                                    const register = () => {
                                        if (!name) alert("Please enter name");
                                        registerWithEmailAndPassword(name, email, password);
                                    };
                                    useEffect(() => {
                                        if (loading) return;
                                        if (user) history.replace("/dashboard");
                                    }, [user, loading]);
                                    return (
                                        <div className="register">
                                        <div className="register__container">
                                            <input
                                            type="text"
                                            className="register__textBox"
                                            value={name}
                                            onChange={(e) => setName(e.target.value)}
                                            placeholder="Full Name"
                                            />
                                            <input
                                            type="text"
                                            className="register__textBox"
                                            value={email}
                                            onChange={(e) => setEmail(e.target.value)}
                                            placeholder="E-mail Address"
                                            />
                                            <input
                                            type="password"
                                            className="register__textBox"
                                            value={password}
                                            onChange={(e) => setPassword(e.target.value)}
                                            placeholder="Password"
                                            />
                                            <button className="register__btn" onClick={register}>
                                            Register
                                            </button>
                                            <button
                                            className="register__btn register__google"
                                            onClick={signInWithGoogle}
                                            >
                                            Register with Google
                                            </button>
                                            <div>
                                            Already have an account? <Link to="/">Login</Link> now.
                                            </div>
                                        </div>
                                        </div>
                                    );
                                    }
                                    export default Register;

<!-- ---------------------------------------------Register.css------------------------------------------------------>

Create a new component called Register to handle user registrations. Here are the styles for Register.css:

                                            .register {
                                            height: 100vh;
                                            width: 100vw;
                                            display: flex;
                                            align-items: center;
                                            justify-content: center;
                                            }
                                            .register__container {
                                            display: flex;
                                            flex-direction: column;
                                            text-align: center;
                                            background-color: #dcdcdc;
                                            padding: 30px;
                                            }
                                            .register__textBox {
                                            padding: 10px;
                                            font-size: 18px;
                                            margin-bottom: 10px;
                                            }
                                            .register__btn {
                                            padding: 10px;
                                            font-size: 18px;
                                            margin-bottom: 10px;
                                            border: none;
                                            color: white;
                                            background-color: black;
                                            }
                                            .register__google {
                                            background-color: #4285f4;
                                            }
                                            .register div {
                                            margin-top: 7px;
                                            }

                                            Here, we are using similar approach as we used in the Login component. We are just using the functions we previously created in firebase.js. Again, here we are using useEffect along with react-firebase-hooks to keep track of user authentication status.

> > Reset

        -- Let’s look at resetting passwords. Create a new component called Reset

<!-------------------------------------------------Reset.js--------------------------------------------------------->

                                            import React, { useEffect, useState } from "react";
                                            import { useAuthState } from "react-firebase-hooks/auth";
                                            import { useNavigate } from "react-router-dom";
                                            import { Link } from "react-router-dom";
                                            import { auth, sendPasswordResetEmail } from "./firebase";
                                            import "./Reset.css";
                                            function Reset() {
                                            const [email, setEmail] = useState("");
                                            const [user, loading, error] = useAuthState(auth);
                                            const navigate = useNavigate();
                                            useEffect(() => {
                                                if (loading) return;
                                                if (user) navigate("/dashboard");
                                            }, [user, loading]);
                                            return (
                                                <div className="reset">
                                                <div className="reset__container">
                                                    <input
                                                    type="text"
                                                    className="reset__textBox"
                                                    value={email}
                                                    onChange={(e) => setEmail(e.target.value)}
                                                    placeholder="E-mail Address"
                                                    />
                                                    <button
                                                    className="reset__btn"
                                                    onClick={() => sendPasswordResetEmail(email)}
                                                    >
                                                    Send password reset email
                                                    </button>
                                                    <div>
                                                    Don't have an account? <Link to="/register">Register</Link> now.
                                                    </div>
                                                </div>
                                                </div>
                                            );
                                            }
                                            export default Reset;

                                             -- Similar to what we did for the Login and Register components.

<!-- -------------------------------------------------------------Reset.css---------------------------------------------------------- -->

                                                .reset {
                                                height: 100vh;
                                                width: 100vw;
                                                display: flex;
                                                align-items: center;
                                                justify-content: center;
                                                }
                                                .reset__container {
                                                display: flex;
                                                flex-direction: column;
                                                text-align: center;
                                                background-color: #dcdcdc;
                                                padding: 30px;
                                                }
                                                .reset__textBox {
                                                padding: 10px;
                                                font-size: 18px;
                                                margin-bottom: 10px;
                                                }
                                                .reset__btn {
                                                padding: 10px;
                                                font-size: 18px;
                                                margin-bottom: 10px;
                                                border: none;
                                                color: white;
                                                background-color: black;
                                                }
                                                .reset div {
                                                margin-top: 7px;
                                                }

> > Dashboard

                --Let’s focus on the dashboard. Create a new component called Dashboard

<!-- -------------------------------------------------Dashboard.js----------------------------------------------------- -->

import React, { useEffect, useState } from "react";
import { useAuthState } from "react-firebase-hooks/auth";
import { useNavigate } from "react-router-dom";
import "./Dashboard.css";
import { auth, db, logout } from "./firebase";
import { query, collection, getDocs, where } from "firebase/firestore";
function Dashboard() {
const [user, loading, error] = useAuthState(auth);
const [name, setName] = useState("");
const navigate = useNavigate();
const fetchUserName = async () => {
try {
const q = query(collection(db, "users"), where("uid", "==", user?.uid));
const doc = await getDocs(q);
const data = doc.docs[0].data();
setName(data.name);
} catch (err) {
console.error(err);
alert("An error occured while fetching user data");
}
};
useEffect(() => {
if (loading) return;
if (!user) return navigate("/");
fetchUserName();
}, [user, loading]);
return (

<div className="dashboard">
<div className="dashboard__container">
Logged in as
<div>{name}</div>
<div>{user?.email}</div>
<button className="dashboard__btn" onClick={logout}>
Logout
</button>
</div>
</div>
);
}
export default Dashboard;

We are checking the authentication state. If the user is not authenticated, we redirect the user to the login page.

<!-- ----------------------------------------------------------Dashboard.css------------------------------------------------------ -->

.dashboard {
height: 100vh;
width: 100vw;
display: flex;
align-items: center;
justify-content: center;
}
.dashboard**container {
display: flex;
flex-direction: column;
text-align: center;
background-color: #dcdcdc;
padding: 30px;
}
.dashboard**btn {
padding: 10px;
font-size: 18px;
margin-top: 10px;
border: none;
color: white;
background-color: black;
}
.dashboard div {
margin-top: 7px;
}

> > App.js

<!-- -----------------------------------------App.js----------------------------------------------- -->

Lastly, let’s add everything to the router. Your App.js should look like this:

import "./App.css";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Login from "./Login";
import Register from "./Register";
import Reset from "./Reset";
import Dashboard from "./Dashboard";
function App() {
return (

<div className="app">
<Router>
<Routes>
<Route exact path="/" element={<Login />} />
<Route exact path="/register" element={<Register />} />
<Route exact path="/reset" element={<Reset />} />
<Route exact path="/dashboard" element={<Dashboard />} />
</Routes>
</Router>
</div>
);
}
export default App;

The app is fully functional!
