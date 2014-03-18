# Topics to Cover
- arrayComputed
- visually demonstrate filterBy vs filtering via @each.prop
- @this
- non-array-deps
- .[]
- writing a custom array computed
- reduceComputed


# Theme

Event logs
  - (source ϵ {web,db}, user ϵ {dduck, mmouse}, priority, msg)
  - filterBy (persisted, true)
    - visually show with willDestroy highlight? (do this manually)
  - @this (include your role on project: user -> you)
  - .[] source checkboxes (web, db)
  - custom array computed (briefly look at EmberCPM.concat)


# Outline

Building part of an app that filters a list that periodically gains new
elements.  Here's a simple look at some of the code (view, controller).  You
come to notice that's there's a lot more DOM churn than you would have expected.
When you debug on subtree you notice that whenever you add an item to the
upstream, unfiltered array, the entire table is torn down and rebuilt a row at a
time (visualise screenshots/willDestroyElement).

If we consider how normal cp works it's because the computed result is treated
as a single value that is either valid or completely invalidated: so when we
modify the array the entire cp is rebuilt.  Adding n items results in the filter
recomputed n times, but of course the fundamental problem requires only that we
partially recompute the property when the upstream array is only partially
mutated.

This is what arrayComputed properties do.  So we swap out our cp for one using
`Ember.computed.filterBy`, which is an array computed macro, and now when we add
items we don't need to completely rebuild the table.

So far all is well for something simple where we can just match a single
property against a constant.  This is a common enough case that our helper is
justified, but let's add a filter with some interaction.  Here we add something
to filter messages by prefix, case-insensitive.  But now we actually do want to
invalidate the entire computed array when the filter is changed, but keep our
one-at-a-time semantics when we mutate the upstream array.  Fortunately this is
just how array computed works: non-array dependencies completely invalidate the
computed result, like normal single-value CPs, whereas array dependencies only
partially invalidate the result.

As an aside, the above is a simplification: when the array dependency is set it
will also invalidate the cp, it's just that it will also have array observers to
handle mutations.


Okay so there's a bunch of built-in array computed macros and you can find them
at the API docs under Ember.computed, but the macros exist really just to handle
common cases.  


