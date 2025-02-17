<div className="container">
  <div className="row row-cols-1 row-cols-sm-2 row-cols-md-3 row-cols-lg-6 g-3 mt-4">
    {displayedItems.map((reward) => (
      <div key={reward.id} className="col d-flex">
        <RewardsCard reward={reward} cardOptions={randomCardOptions} />
      </div>
    ))}
  </div>
</div>