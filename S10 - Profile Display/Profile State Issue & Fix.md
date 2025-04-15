# Bug With User Profile & New User Registration

We found a security flaw in this app. If a guest user browses a dev profile and then registers, the browsed users profile data is still in the "profile" state and the newly registered user then sees and can edit the users info.

There are many ways to handle this, the easiest being to clear that "profile" state when no profile is found for the new user. 

In the profile reducer (reducers/profile.js), in the PROFILE_ERROR case, add profile: null

```
case PROFILE_ERROR:
	return {
	...state,
		error: payload,
		loading: false,
		profile: null // Add this
	};
```

or in the error handler for  getCurrentProfile() in actions/profile.js, dispatch CLEAR_PROFILE which will essentially do the same thing and set profile: null

```
export const getCurrentProfile = () => async dispatch => {
	try {
		const res = await axios.get('/api/profile/me');

		dispatch({
			type: GET_PROFILE,
			payload: res.data
		});
	} catch (err) {
		// Add this
		dispatch({ type: CLEAR_PROFILE });

		dispatch({
			type: PROFILE_ERROR,
			payload: { msg: err.response.statusText, status: err.response.status }
		});
	}
};
```

When I update this course, I will be storing the logged in users profile in a different piece of state than a viewed users profile. Unfortunatley this kind of stuff happens all of the time where you see a better way of doing something after the fact.

Sorry for the bug :(