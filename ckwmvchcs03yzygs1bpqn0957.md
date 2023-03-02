---
title: "Fetching Data In React"
datePublished: Tue Nov 30 2021 14:36:48 GMT+0000 (Coordinated Universal Time)
cuid: ckwmvchcs03yzygs1bpqn0957
slug: fetching-data-in-react-b2ddfa12ccce
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1638316804082/eI9vi1mvQ.jpeg
tags: programming-blogs, javascript, reactjs, reacthooks

---

Photo by [Anthony Duran](https://unsplash.com/@fightmorefilms?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

The React Docs are great and getting better, but I have always felt it lacked a basic explanation of how to fetch data. So much is devoted to state and props; we don’t get into where that state and props come from.

Some great libraries, like React-Query, help fetch data in a React app, but, unfortunately, we don’t at least have some basic concept of data fetching before we bring in a 3rd party library that we may or may not need.

For this reason, I thought I would quickly go over some basics regarding data fetching and some opinions that I like to follow.

### The Basics

There are two essential parts to data fetching: We need to trigger an action to get the data, and we need to store that data somewhere. This means we need to use `useEffect` and `useState` to handle those two-part. This is what that looks like:

```javascript
const UserCard = () =>{  
  const [user, setUser] = useState()  
    
  useEffect(()=>{   
    fetch('/api/user/travis')  
     .then(res=>res.ok ? res.json() : Promise.reject(res))  
     .then(setUser)  
  })  
   
  return (/* render card here */)   
    
}
```

In the above component, we have state that is updated when we get a user back from our API. That is the bare minimum we need when fetching data in a React app. Now chances are we want our Card to be a bit more dynamic, so lets, quickly update it to take any user:

```javascript
const UserCard = ({ userName }) =>{  
  const [user, setUser] = useState()  
    
  useEffect(()=>{   
    fetch(`/api/user/${userName}`)  
     .then(res=>res.ok ? res.json() : Promise.reject(res))  
     .then(setUser)  
  },[userName])  
   
  return (/* render card here */)   
    
}
```

With this update, we will get the user data of whichever `userName` is passed in. If the `userName` prop changes, we will fetch new data and set it to state when it returns.

This might not seem like much, but this is all you need to know to start fetching data in a React Component. However, I think it would be helpful to share my opinions on some of my personal best practices.

### Move the ‘fetching’ into its own function

The “how” we get our data is not crucial to our React Component. All it cares about is that it requests the data and what to do when it comes back. There is also much more you need to do with the returned data than what my examples show. One typically gets more data back than you need. Also, sometimes the data you need requires multiple API calls. API’s also change, and maintaining that across your app can get complicated if each React component knows too much about how the data is fetched.

For this reason, I prefer to move the implementation of the fetching into its own function, typically in an `api` folder for organization purposes:

```javascript
function getUserData(userName){  
   return fetch(`/api/user/${userName}`)  
     .then(res=> res.ok ? res.json() : Promise.reject(res))  
     .then(({fullName, images, bio})=>({  
       name:fullName,  
       avatar:images.large,  
       bio,   
     }))  
}
```

Now our UserCard can be updated like this:

```javascript
const UserCard = ({ userName }) =>{  
  const [user, setUser] = useState()  
    
  useEffect(()=>{   
    getUserData(userName)  
     .then(setUser)  
  },[userName])  
   
  return (/* render card here */)   
    
}
```

This is much easier to reason about in our Component and is more resilient if we change our back-end API.

### Handle all the states

Fetching data is not as simple as getting data and showing it. When you are fetching data, you are always in one of three states. You are either loading data, have the data, or there was an error fetching the data.

So it would be best if you handled all those states in your component, for example:

```javascript
const UserCard = ({ userName }) =>{  
  const [user, setUser] = useState()  
  const [loading, setLoading] = useState(true)  
  const [error, setError] = useState()  
   
  useEffect(()=>{   
    setLoading(true)  
    setError()  
   
    getUserData(userName)  
     .then(setUser)  
     .catch(setError)  
     .finally(()=>setLoading(false))  
   
  },[userName])

  if(error){  
   return (/* error state */)  
  }

  if(loading){  
   return (/* loading state */)  
  }

  return (/* render card here */)   
    
}
```

In our `useEffect` we start by initializing our loading and error states. We do this so that we will set our states back to their initial states and then fetch the data if the prop changes. On top of that, we added a `catch` and `finally` to our promise chain. If there is an error, we will catch that and set the error state. We use `finally` so that we will set the loading state to false no matter if we have an error.

There is also another state that we often forget about. This is what do we do if the component unmounts before our data gets back? We will have an error if we try to update any of our states after the component has unmounted. Also, what do we do if we change the prop multiple times and send off multiple requests? How do we know if we are updating the state with the correct user since we have no control over which data will return in which order? Luckily we can refactor our code to handle both of these circumstances.

```javascript
const UserCard = ({ userName }) =>{  
  const [fetchState, setFetchState] = useState({ loading:true })

  const {user, error, loading} = fetchState  

  useEffect(()=>{   
    let shouldSet = true

    setFetchState({ loading: true })  
   
    getUserData(userName)  
     .then(user => ({ user }))  
     .catch(error => ({ error }))  
     .then(state=>{  
      if (shouldSet) setFetchState({...state, loading:false})  
     })

    return () => {  
      shouldSet = false  
    }  
   
  },[userName])

  if(error){  
   return (/* error state */)  
  }

  if(loading){  
   return (/* loading state */)  
  }

  return (/* render card here */)   
    
}
```

In this refactoring, we are merging all three states into a single state object. This doesn’t just make things more convenient for us later on, but it also makes sense. Any time we are calling multiple setState functions, chances are we should be grouping those together. These states don’t work in isolation but are part of one state and should be updated together.

Next in our `useEffect`, we are initializing a `shouldSet` variable to true. Then we instead of setting the state in our `.then` or `.catch` we are returning a partial state with either the user or the error. In our final `.then` we then verify if the `shouldSet` variable is still true. If it is, we update the state with our partial state and set loading to false.

This makes this work because we are now returning a cleanup function that will change the value of `shouldUpdate` to false. This will be set to false if the component unmounts or if the `userName` changes and the effect is rerun. Either way, we won't update the wrong data or update at all if we don’t want to.

### You probably want to make a custom hook

Once again, all this logic is starting to make our card cluttered. Also, the `UserCard` component doesn’t care how we get the states we do, only with what the states are. So it makes sense to refactor this out into a custom hook:

```javascript
function useCardState(userName){  
  const [fetchState, setFetchState] = useState({ loading:true })

  useEffect(()=>{   
    let shouldSet = true

    setFetchState({ loading: true })  
   
    getUserData(userName)  
     .then(user => ({ user }))  
     .catch(error => ({ error }))  
     .then(state=>{  
      if (shouldSet) setFetchState({...state, loading:false})  
     })

    return () => {  
      shouldSet = false  
    }   
  }, [userName])

 return fetchState  
}
```

We have moved all that logic state into a single hook that takes a `userName` and returns a the `fetchState.` Now we can use that in our component like this:

```javascript
const UserCard = ({ userName }) => {

  const {user, error, loading} = useCardState(userName)

  if(error){  
    return (/* error state */)  
  }

  if(loading){  
    return (/* loading state */)  
  }

  return (/* render card here */)   
    
}

```

Now the `UserCard` is easier to reason about again, and we can compartmentalize how we fetch that data and what to do while we fetched our data. It’s important to point out that this hook doesn’t need to be in another file and or made “reusable” at this point. As you go, if you find a common pattern that solves a problem, then go ahead and abstract it generally.

These are the basics of how to do data fetching in React and some good strategies to solve common problems associated with it. Hopefully, this helps fill the gap in your React apps.