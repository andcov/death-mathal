<script>
  import { Router, Link, Route } from "svelte-routing";
  import AlgebraQuestions from "./routes/AlgebraQuestions.svelte";
  import Equations from "./routes/Equations.svelte";
  import About from "./routes/About.svelte";

  export let url = "/death-mathal/";
</script>

<Router {url}>
  <nav>
    <Link to="/death-mathal/algebra">Algebraic Fomulas</Link>
    <Link to="/death-mathal/equations">Basic Equations</Link>
    <Link to="/death-mathal/about">About</Link>
  </nav>
  <div>
    <Route path="/death-mathal/" component={About} />
    <Route path="/death-mathal/about" component={About} />
    <Route path="/death-mathal/algebra" component={AlgebraQuestions} />
    <Route path="/death-mathal/equations" component={Equations} />
  </div>
</Router>
