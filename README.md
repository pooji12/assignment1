package store

import (
    "log"
    "net/http"
    "github.com/pooji12/assignment1"
)

var controller = &Controller{Repository: Repository{}}


type Route struct {
    Name        string
    Method      string
    Pattern     string
    HandlerFunc http.HandlerFunc
}


type Routes []Route

var routes = Routes {
    Route {
        "Authentication",
        "POST",
        "/get-token",
        controller.GetToken,
    },
    Route {
        "Index",
        "GET",
        "/",
        controller.Index,
    },
    Route {
        "AddProduct",
        "POST",
        "/AddProduct",
        AuthenticationMiddleware(controller.AddProduct),
    },
    Route {
        "UpdateProduct",
        "PUT",
        "/UpdateProduct",
        AuthenticationMiddleware(controller.UpdateProduct),
    },
    
    Route {
        "GetProduct",
        "GET",
        "/products/{id}",
        controller.GetProduct,
    },
    
    Route {
        "DeleteProduct",
        "DELETE",
        "/deleteProduct/{id}",
        AuthenticationMiddleware(controller.DeleteProduct),
    },
    
    Route {
        "SearchProduct",
        "GET",
        "/Search/{query}",
        controller.SearchProduct,
    }}


func NewRouter() *mux.Router {
    router := mux.NewRouter().StrictSlash(true)
    for _, route := range routes { 
        var handler http.Handler
        log.Println(route.Name)
        handler = route.HandlerFunc
        
        router.
         Methods(route.Method).
         Path(route.Pattern).
         Name(route.Name).
         Handler(handler)
    }
    return router
}
