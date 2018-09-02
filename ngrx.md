
# NGRX

### Prerequisites 

install Node JS

install yarn (Optional)

update npmrc/ yarnrc

install Angular Cli Globally

### Create Angular Cli seed project 

```
ng new my-first-ngrx
cd my-first-ngrx
ng serve
```

### Install ngrx dependency
```
npm install --save @ngrx/store
```
 
With Optional :
```
npm install --save @ngrx/store @ngrx/effects @ngrx/store-devtools
```
### Create new Component 

```
ng g component todo
ng g service todo/todo
 ```
 
### Inject service into todoComponent
For nice looks & feel update angular.json

```
npm install bootstrap --save
 
 
"styles": [
"node_modules/bootstrap/dist/css/bootstrap.min.css",
"src/styles.css"
],
```
### Add todo model
todo.model.ts
 
 ```
export class Todo {
    id: number;
    title = '';
    complete = false;
}
```
### Add reducer
/todo/todo.reducer.ts

```
export const todos = (state = [], action) => {
    switch (action.type) {
      case 'ADD_TODO':
        return [...state, action.payload];
      case 'REMOVE_TODO':
        return state.filter(todo => todo.id !== action.payload.id);
      case 'TOGGLE_COMPLETE':
        return state.map(todo => {
          if (todo.id === action.payload.id) {
            return Object.assign({}, todo, {
              complete: !todo.complete
            });
          }
          return todo;
        });
      default:
        return state;
    }
  };
 ```
### Update app.module.ts

```
import { StoreModule } from '@ngrx/store';
import { todos } from './todo/todo.reducer';
import { StoreDevtoolsModule } from '@ngrx/store-devtools';
 
 
imports: [
    ....
    StoreModule.forRoot({ todos }),
    StoreDevtoolsModule.instrument({
      maxAge: 25,
    })
    ....
  ],
  
 ```
### Update app.component.html
```
<app-todo></app-todo>
```
### Update todo.service.ts

```
// Add new property
    public lastId = 0;
    public todos: Todo[] = [];
 
``` 
 
 ```
// update constructor
  constructor(private _store: Store<any>) {
    _store.select('todos').subscribe(todos => {
      this.todos = todos;
    });
  }
 ```
 
 ```
public addTodo(todo: Todo): void {
    this._store.dispatch({
      type: 'ADD_TODO',
      payload: {
        id: ++this.lastId,
        title: todo.title,
        complete: todo.complete
      }
    });
  }
public deleteTodoById(todoId: number): void {
    this._store.dispatch({
      type: 'REMOVE_TODO',
      payload: { id: todoId }
    });
  }
public toggleTodoComplete(todoId: number): void {
    this._store.dispatch({
      type: 'TOGGLE_COMPLETE',
      payload: { id: todoId }
    });
  }
public getCompleteTodos(): Todo[] {
    return this.todos.filter(todo => todo.complete === true);
  }
public getIncompleteTodos(): Todo[] {
    return this.todos.filter(todo => todo.complete === false);
  }
  
 ```
 
### Update todo.component.ts
```
// new property
newTodo: Todo = new Todo();
constructor(private todoService: TodoService) { }
public addTodo(): void {
  this.todoService.addTodo(this.newTodo);
}
 
 
public toggleTodoComplete({ id }): void {
  this.todoService.toggleTodoComplete(id);
}
public removeTodo({ id }): void {
  this.todoService.deleteTodoById(id);
}
 
public allTodos(): number {
  return this.incompleteTodos.length + this.completeTodos.length;
}
public get incompleteTodos(): Array<Todo> {
  return this.todoService.getIncompleteTodos();
}
 
public get completeTodos(): Array<Todo> {
  return this.todoService.getCompleteTodos();
}
```

### Update todo.component.html
```
<section class="container">
  <header>
    <h1>Angular todo app</h1>
    <div class="form-group">
      <div class="form-row">
        <div class="col-6">
          <input class="form-control" placeholder="What needs to be done?" [(ngModel)]="newTodo.title" (keyup.enter)="addTodo()">
        </div>
      </div>
    </div>
  </header>
  <section *ngIf="incompleteTodos.length > 0">
    <ng-container>
      <div class="form-group" *ngFor="let todo of incompleteTodos">
        <div class="form-row align-items-center">
          <div class="col-4">
            <div class="custom-control custom-checkbox">
              <input type="checkbox" class="custom-control-input" id="customCheck{{todo.id}}" (click)="toggleTodoComplete(todo)" [checked]="todo.complete">
              <label class="custom-control-label" for="customCheck{{todo.id}}">{{todo.title}}</label>
            </div>
          </div>
          <div class="col-2">
            <button class="btn btn-secondary btn-sm" (click)="removeTodo(todo)">remove</button>
          </div>
        </div>
      </div>
    </ng-container>
  </section>
  <section *ngIf="completeTodos.length > 0">
    <h2>Done</h2>
    <ng-container>
      <div class="form-group" *ngFor="let todo of completeTodos">
        <div class="form-row align-items-center">
          <div class="col-4">
            <div class="custom-control custom-checkbox">
              <input type="checkbox" class="custom-control-input" id="customCheck{{todo.id}}" (click)="toggleTodoComplete(todo)" [checked]="todo.complete">
              <label class="custom-control-label" for="customCheck{{todo.id}}">{{todo.title}}</label>
            </div>
          </div>
          <div class="col-2">
            <button class="btn btn-secondary btn-sm" (click)="removeTodo(todo)">remove</button>
          </div>
        </div>
      </div>
    </ng-container>
  </section>
  <footer *ngIf="allTodos() > 0">
    <div class="row">
      <div class="col-sm">
        <strong>{{allTodos()}}</strong>
        {{allTodos() == 1 ? 'item' : 'items'}} in list
      </div>
      <div class="col-sm">
        <strong>{{completeTodos.length}}</strong>
        {{completeTodos.length == 1 ? 'item' : 'items'}} complete
      </div>
      <div class="col-sm">
        <strong>{{incompleteTodos.length}}</strong>
        {{incompleteTodos.length == 1 ? 'item' : 'items'}} incomplete
      </div>
    </div>
  </footer>
</section>
```

