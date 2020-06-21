<script>
  import { timer } from "rxjs";
  let tick = timer(0, 1000);
  /* For Subjects just
  const subject = new BehaviorSubject('')
  subject.set = subject.next
  and In Template use $subject
  */
</script>

{$tick}
