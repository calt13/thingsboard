#### HTML template of dialog to edit a device or an asset

```html
{:code-style="max-height: 400px;"}
<form #editEntityForm="ngForm" [formGroup]="editEntityFormGroup"
      (ngSubmit)="save()"  class="edit-entity-form">
  <mat-toolbar class="flex flex-row" color="primary">
    <h2>Edit {{entityType.toLowerCase()}} {{entityName}}</h2>
    <span class="flex-1"></span>
    <button mat-icon-button (click)="cancel()" type="button">
      <mat-icon class="material-icons">close</mat-icon>
    </button>
  </mat-toolbar>
  <mat-progress-bar color="warn" mode="indeterminate" *ngIf="isLoading$ | async">
  </mat-progress-bar>
  <div style="height: 4px;" *ngIf="!(isLoading$ | async)"></div>
  <div mat-dialog-content class="flex flex-col">
    <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
      <mat-form-field class="mat-block flex-1">
        <mat-label>Entity Name</mat-label>
        <input matInput formControlName="entityName" required readonly="">
      </mat-form-field>
      <mat-form-field class="mat-block flex-1">
        <mat-label>Entity Label</mat-label>
        <input matInput formControlName="entityLabel">
      </mat-form-field>
    </div>
    <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
      <mat-form-field class="mat-block flex-1">
        <mat-label>Entity Type</mat-label>
        <input matInput formControlName="entityType" readonly>
      </mat-form-field>
      <mat-form-field class="mat-block flex-1">
        <mat-label>Type</mat-label>
        <input matInput formControlName="type" readonly>
      </mat-form-field>
    </div>
    <div formGroupName="attributes" class="flex flex-col">
      <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
        <mat-form-field class="mat-block flex-1">
          <mat-label>Latitude</mat-label>
          <input type="number" step="any" matInput formControlName="latitude">
        </mat-form-field>
        <mat-form-field class="mat-block flex-1">
          <mat-label>Longitude</mat-label>
          <input type="number" step="any" matInput formControlName="longitude">
        </mat-form-field>
      </div>
      <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
        <mat-form-field class="mat-block flex-1">
          <mat-label>Address</mat-label>
          <input matInput formControlName="address">
        </mat-form-field>
        <mat-form-field class="mat-block flex-1">
          <mat-label>Owner</mat-label>
          <input matInput formControlName="owner">
        </mat-form-field>
      </div>
      <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
        <mat-form-field class="mat-block flex-1">
          <mat-label>Integer Value</mat-label>
          <input type="number" step="1" matInput formControlName="number">
          <mat-error *ngIf="editEntityFormGroup.get('attributes.number').hasError('pattern')">
            Invalid integer value.
          </mat-error>
        </mat-form-field>
        <div class="boolean-value-input flex flex-1 flex-col items-start justify-center">
          <label class="checkbox-label">Boolean Value</label>
          <mat-checkbox formControlName="booleanValue" style="margin-bottom: 40px;">
            {{ (editEntityFormGroup.get('attributes.booleanValue').value ? "value.true" : "value.false") | translate }}
          </mat-checkbox>
        </div>
      </div>
    </div>
    <div class="relations-list old-relations">
      <div class="mat-body-1" style="padding-bottom: 10px; color: rgba(0,0,0,0.57);">Relations</div>
      <div class="body" [class.!hidden]="!oldRelations().length">
        <div class="row flex flex-row items-center justify-start" formArrayName="oldRelations"
             *ngFor="let relation of oldRelations().controls; let i = index;">
          <div [formGroupName]="i" class="mat-elevation-z2 flex flex-1 flex-row" style="padding: 5px 0 5px 5px;">
            <div class="flex flex-1 flex-col">
              <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
                <mat-form-field class="mat-block" style="min-width: 100px;">
                  <mat-label>Direction</mat-label>
                  <mat-select formControlName="direction" name="direction">
                    <mat-option *ngFor="let direction of entitySearchDirection | keyvalue" [value]="direction.value">
                      {{ ("relation.search-direction." + direction.value) | translate}}
                    </mat-option>
                  </mat-select>
                  <mat-error *ngIf="relation.get('direction').hasError('required')">
                    Relation direction is required.
                  </mat-error>
                </mat-form-field>
                <tb-relation-type-autocomplete
                  class="mat-block flex-1"
                  formControlName="relationType"
                  required="true">
                </tb-relation-type-autocomplete>
              </div>
              <div class="flex flex-row xs:flex-col">
                <tb-entity-select
                  class="mat-block flex-1"
                  required="true"
                  formControlName="relatedEntity">
                </tb-entity-select>
              </div>
            </div>
            <div class="flex flex-col items-center justify-center">
              <button mat-icon-button color="primary"
                      aria-label="Remove"
                      type="button"
                      (click)="removeOldRelation(i, relation.value)"
                      matTooltip="Remove relation"
                      matTooltipPosition="above">
                <mat-icon>close</mat-icon>
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="relations-list">
      <div class="mat-body-1" style="padding-bottom: 10px; color: rgba(0,0,0,0.57);">New Relations</div>
      <div class="body" [class.!hidden]="!relations().length">
        <div class="row flex flex-row items-center justify-start" formArrayName="relations" *ngFor="let relation of relations().controls; let i = index;">
          <div [formGroupName]="i" class="mat-elevation-z2 flex flex-1 flex-row" style="padding: 5px 0 5px 5px;">
            <div class="flex flex-1 flex-col">
              <div class="flex flex-row gap-2 xs:flex-col xs:gap-0">
                <mat-form-field class="mat-block" style="min-width: 100px;">
                  <mat-label>Direction</mat-label>
                  <mat-select formControlName="direction" name="direction">
                    <mat-option *ngFor="let direction of entitySearchDirection | keyvalue" [value]="direction.value">
                      {{ ("relation.search-direction." + direction.value) | translate}}
                    </mat-option>
                  </mat-select>
                  <mat-error *ngIf="relation.get('direction').hasError('required')">
                    Relation direction is required.
                  </mat-error>
                </mat-form-field>
                <tb-relation-type-autocomplete
                  class="mat-block flex-1"
                  formControlName="relationType"
                  [required]="true">
                </tb-relation-type-autocomplete>
              </div>
              <div class="flex flex-row xs:flex-col">
                <tb-entity-select
                  class="mat-block flex-1"
                  [required]="true"
                  formControlName="relatedEntity">
                </tb-entity-select>
              </div>
            </div>
            <div class="flex flex-col items-center justify-center">
              <button mat-icon-button color="primary"
                      aria-label="Remove"
                      type="button"
                      (click)="removeRelation(i)"
                      matTooltip="Remove relation"
                      matTooltipPosition="above">
                <mat-icon>close</mat-icon>
              </button>
            </div>
          </div>
        </div>
      </div>
      <div>
        <button mat-raised-button color="primary"
                type="button"
                (click)="addRelation()"
                matTooltip="Add Relation"
                matTooltipPosition="above">
          Add
        </button>
      </div>
    </div>
  </div>
  <div mat-dialog-actions class="flex flex-row items-center justify-end">
    <button mat-button color="primary"
            type="button"
            [disabled]="(isLoading$ | async)"
            (click)="cancel()" cdkFocusInitial>
      Cancel
    </button>
    <button mat-button mat-raised-button color="primary"
            type="submit"
            [disabled]="(isLoading$ | async) || editEntityForm.invalid || !editEntityForm.dirty">
      Save
    </button>
  </div>
</form>
{:copy-code}
```

<br>
<br>
