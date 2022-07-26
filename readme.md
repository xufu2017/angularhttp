start: "start": "concurrently --kill-others \"ng build --watch --no-delete-output-path\" \"node server.js\"",
"serve-favicon": "2.5.0"

 providers: [
    { provide: ErrorHandler, useClass: BookTrackerErrorHandlerService },
    { provide: HTTP_INTERCEPTORS, useClass: LogResponseInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: AddHeaderInterceptor, multi: true },
    { provide: HTTP_INTERCEPTORS, useClass: CacheInterceptor, multi: true }
  ],
  
    @Injectable()
    export class AddHeaderInterceptor implements HttpInterceptor {
      intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
        console.log(`AddHeaderInterceptor - ${req.url}`);

        let jsonReq: HttpRequest<any> = req.clone({
          setHeaders: { 'Content-Type': req.context.get(CONTENT_TYPE) }
        });

        return next.handle(jsonReq);
      }

    }
    

    @Injectable()
    export class BookTrackerErrorHandlerService implements ErrorHandler {

      handleError(error: any): void {
        let customError: BookTrackerError = new BookTrackerError();
        customError.errorNumber = 200;
        customError.message = (<Error>error).message;
        customError.friendlyMessage = 'An error occurred. Please try again.';

        console.log(customError);
      }

      constructor() { }

    }
    
    @Injectable({
      providedIn: 'root'
    })
    export class BooksResolverService implements Resolve<Book[] | BookTrackerError> {

      constructor(private dataService: DataService) { }

      resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Book[] | BookTrackerError> {
        return this.dataService.getAllBooks()
          .pipe(
            catchError(err => of(err))
          );
      }

    }
