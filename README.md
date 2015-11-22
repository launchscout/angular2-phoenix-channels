# angular2-phoenix-channels

A package that lets you use Phoenix channels as Angular 2 Observables.

## Configuration

When you bootstrap your app, you'll need to setup a provider and pass
in your websocket uri.

```javascript
let phoenixChannelsProvider = provide(PhoenixChannels, { useFactory: () => {
  return new PhoenixChannels("ws://localhost:4000/socket");
} });

bootstrap(MyAppComponent, [phoenixChannelsProvider]);
```

## Usage

Have your service injected with PhoenixChannels

```typescript
@Injectable()
class CandidateService {
  constructor(http: Http, phoenixChannels: PhoenixChannels) {
  ...
  }
}
```

Join a channel.

```javascript
let allCandidatesChannel = this.phoenixChannels.channel("candidates:all");
allCandidatesChannel.join();
```

Observe messages. `observeMessage` returns an observable can call subscribe
or any other observable operators on.
```javascript
let phoenixObservable = this.allCandidatesChannel.observeMessage("change").map( (resp) => resp.candidates );
```

You can merge this observable with other observables.

```javascript
getCandidates() {
  let httpObservable = this.http.get('http://localhost:4000/candidates').map( (resp) => resp.json());
  let phoenixObservable = this.allCandidatesChannel.observeMessage("change").map( (resp) => resp.candidates );
  return Observable.merge(httpObservable, phoenixObservable);
}
```

## Examples
Angular 2 app: http://github.com/gaslight/campg-angular2.git
Phoenix server for above app: http://github.com/superchris/hirem_server.git
