# golangtutorial
My Golang learnings

## REST Basics
-Import "github.com/gorilla/mux"
-Import "encoding/json"

```
//Creating a struct corresponding to mux.Router details
type Route struct {
	Name string
	Method string
	Path string
	Handler http.HandlerFunc
}
```
```
//Populating routes with details
type Routes []Route
var routes = Routes {
	Route {
		"getEmployees",
		"GET",
		"/employee",
		getEmployees,
	},
	Route{
		"getEmployeeById",
		"GET",
		"/employee/{id}",
		getEmployeeById,
	},
}
```
```
//Creating an Employee struct, followed by an array of Employee struct as type and then as variable and adding entries in it
type Employee struct {
	Id string `json:"id"`
	FirstName string `json:"firstName"`
	LastName string `json:"lastName"`
}
type Employees []Employee
var employees = Employees {
	Employee {Id: "1", FirstName: "Foo", LastName: "Bar"},
	Employee {Id: "2", FirstName: "Baz", LastName: "Qux"},
}
```

```
//Adding functions which act as http.HandlerFunc
func getEmployees(w http.ResponseWriter, r *http.Request) {

	json.NewEncoder(w).Encode(employees)
}

func getEmployeeById(w http.ResponseWriter, r *http.Request) {
	params :=mux.Vars(r)
	id := params["id"]
	for _, employee := range employees {
		if employee.Id == id {
			if err := json.NewEncoder(w).Encode(employee); err != nil {
				log.Print("error getting requested employee :: ", err)
			}
		}
	}
}
```
```
//Adding routes to mux.Router for ListenAndServe
func AddRoutes(router *mux.Router) *mux.Router {
	for _, route := range routes {
		router.Methods(route.Method).Name(route.Name).Path(route.Path).Handler(route.Handler)
	}
	return router
}
```
```
//main function, which has new muxRouter, call to function to populate routes, call to http.ListenAndServe with port and handler
func main() {
	muxRouter :=mux.NewRouter().StrictSlash(true)
	router := AddRoutes(muxRouter)
	err := http.ListenAndServe(":9095", router)
	if err != nil {
		log.Fatal("error starting http server :: ", err)
		return
	 }
}
```
### //For reading request parameter from request body for a POST request (r)
```
employee := Employee{}
json.NewDecoder(r.Body).Decode(&employee)
OR
var result map[string]interface{}
data, err := ioutil.ReadAll(r.Body)
json.Unmarshal([]byte(data), &result)
```
### Adding elements to slice
```
myslice := []string{"a","'","test2"}
//[a ' test2]

myslice = append(myslice[:3],"b")
//[a ' test2 b]
myslice = append(myslice[:1], myslice[2:]...)
//[a test2 b]
```
