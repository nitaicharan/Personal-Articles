# ChangeDetectionStrategy

Created: May 24, 2022 8:11 AM
Finished: Yes
Finished 📅: June 4, 2022
Source: https://angular.io/api/core/ChangeDetectionStrategy
Tags: #angular

# ****ChangeDetectionStrategy****

The strategy that the default change detector uses to detect changes. When set, takes effect the next time change detection is triggered.

## ****See also****

• [Change detection usage](https://angular.io/api/core/ChangeDetectorRef#usage-notes)

```tsx
enum ChangeDetectionStrategy {
  OnPush: 0
  Default: 1
}
```

## ****Members****

| Member | Description |
| --- | --- |
| OnPush: 0 | Use the CheckOnce
 strategy, meaning that automatic change detection is deactivated until reactivated by setting the strategy to Default
 (CheckAlways
). Change detection can still be explicitly invoked. This strategy applies to all child directives and cannot be overridden. |
| Default: 1 | Use the default CheckAlways
 strategy, in which change detection is automatic until explicitly deactivated. |