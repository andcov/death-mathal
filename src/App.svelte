<script>
  import { Router, Link, Route } from "svelte-routing";
  import AlgebraQuestions from "./routes/AlgebraQuestions.svelte";
  import Equations from "./routes/Equations.svelte";

  export let url = "/";
</script>

<Router {url}>
  <nav>
    <Link to="/algebra">Algebraic Fomulas</Link>
    <Link to="/equations">Basic Equations</Link>
  </nav>
  <div>
    <Route path="/" component={AlgebraQuestions} />
    <Route path="/algebra" component={AlgebraQuestions} />
    <Route path="/equations" component={Equations} />
  </div>
</Router>
