# JWT

1. npm install jsonwebtoken

## 2. generate key set token

node
require('crypto').randomBytes(64).toString('hex')
.env.local => Set ACCESS_TOKEN

## 3. create backend Post Method

        app.post('/jwt', (req, res) => {
            const user = req.body;
            const token = jwt.sign(user, process.env.ACCESS_TOKEN, { expiresIn: '1h' })
            res.send({ token })
        })
## 4. create client post method onAuthStateChange & LocalStorage Save

```
                if(currentUser){
                    axios.post('http://localhost:5000/jwt' , {email:currentUser.email})
                    .then(data=>{
                        localStorage.setItem('access-token' , data.data.token)
                    })
                }else{
                    localStorage.removeItem('access-token')
                }
```

# 5. Verify JWT

## 5.1 create verify function on backend 

```
const verifyJWT = (req, res, next) => {
    const authorization = req.headers.authorization;
    if (!authorization) {
        return res.status(401).send({ error: true, message: 'unauthorized access' });
    }
    // bearer token
    const token = authorization.split(' ')[1];

    jwt.verify(token, process.env.ACCESS_TOKEN, (err, decoded) => {
        if (err) {
            return res.status(401).send({ error: true, message: 'unauthorized access' })
        }
        req.decoded = decoded;
        next();
    })
}
```

## 5.2 use verifyJWT where app.get method is user 
```
        app.get('/carts', verifyJWT, async (req, res) => {
```
### and then verify email 
```
            const decodedEmail = req.decoded.email;
            if (email !== decodedEmail) {
                return res.status(403).send({ error: true, message: 'forbidden access' })
            }
```
## 5.3 check the token where data is fetch is done
```
    const token = localStorage.getItem('access-token');
    
    const res = await fetch(`http://localhost:5000/carts?email=${user?.email}` , {
                headers: {
                    authorization: `bearer ${token}`
                }
            })
```

