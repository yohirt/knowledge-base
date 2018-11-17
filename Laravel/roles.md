In User.php
```js
public function hasAnyRole($roles)
    {
        if (is_array($roles)) {
            foreach ($roles as $role) {
                if ($this->hasRole($role)) {
                    return true;
                }
            }
        } else {
            if ($this->hasRole($roles)) {
                return true;
            }
        }
        return false;
    }
    public function hasRole($role)
    {
        if ($this->roles()->where('name', $role)->first()) {
            return true;
        }
        return false;
    }

  ```
checkRole.php

```js
public function handle($request, Closure $next)
    {
          if ($request->user() === null) {
            // return response("Insufficient permissions", 401);
            return redirect('/login');
        }
        $actions = $request->route()->getAction();
        $roles = isset($actions['roles']) ? $actions['roles'] : null;

        // if ($request->user()->hasAnyRole($roles) || !$roles) {
        if ($request->user()->hasAnyRole($roles)) {
            return $next($request);
        }
        return redirect('/login');
    }
```
```js
public function postAdminAssignRoles(Request $request)
  {
      $user = User::where('email', $request['email'])->first();
      $user->roles()->detach();
      if ($request['role_user']) {
          $user->roles()->attach(Role::where('name', 'User')->first());
      }
      if ($request['role_author']) {
          $user->roles()->attach(Role::where('name', 'Author')->first());
      }
      if ($request['role_admin']) {
          $user->roles()->attach(Role::where('name', 'Admin')->first());
      }
      return redirect()->back();
  }
  ```
